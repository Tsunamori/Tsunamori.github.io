---
title: WP-Bugku-Reverse
toc: true
date: 2021-06-24 14:44:06
tags: [CTF, WriteUp]
categories: [100 Cyber security, 120 CTF, 123 Reverse ]
---

#### 入门逆向
* 解题思路：丢进IDA Pro，可以直接看到flag内容。（似乎没有什么更好的一键复制的办法，如果无法直接显示十六进制的字符转换，可以选中该行按R）

#### sign_in
* 解题思路：直接jadx-gui打开（很久没用安卓逆向工具了，比较了一番发现jadx用起来还是很省时省力的）
MainActivity里找到关键的Java语句：
```
public void checkPassword(String str) {
    if (str.equals(new String(Base64.decode(new StringBuffer(getFlag()).reverse().toString(), 0)))) {
        showMsgToast("Congratulations !");
    } else {
        showMsgToast("Try again.");
    }
}
private String getFlag() {
    return getBaseContext().getString(R.string.toString);
}
```
跟踪去R里找string.toString:
```
public static final int toString = 2131427360;
```
这里只是为创建的值创建一个位置，真正的值不在这里。从resources.arsc里，按照R中toString的位置，找values文件夹内的strings.xml，搜索toString，得到值，再reverse一下，base64解码。
```
import base64

text = '991YiZWOz81ZhFjZfJXdwk3X1k2XzIXZIt3ZhxmZ'
text_re = text[::-1]
result = base64.b64decode(text_re)
print(result)
```

#### easy_re
* 解题思路：估计是一开始直接搜关键词的太多了，现在做出来的值就是拆成两半而且逆序的字符串了。
1. 用IDA：找到主函数，F5进psudocode。
```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v3; // eax
  __m128i v5; // [esp+0h] [ebp-44h] BYREF
  __int64 v6; // [esp+10h] [ebp-34h]
  int v7; // [esp+18h] [ebp-2Ch]
  __int16 v8; // [esp+1Ch] [ebp-28h]
  char v9[32]; // [esp+20h] [ebp-24h] BYREF

  v5 = _mm_loadu_si128((const __m128i *)&xmmword_413E34);
  v7 = 0;
  v6 = '}FTCTUD';
  v8 = 0;
  printf("欢迎来到DUTCTF呦\n");
  printf("这是一道很可爱很简单的逆向题呦\n");
  printf("输入flag吧:");
  scanf("%s", v9);
  v3 = strcmp(v5.m128i_i8, v9);
  if ( v3 )
    v3 = v3 < 0 ? -1 : 1;
  if ( v3 )
    printf(aFlag_0);
  else
    printf(aFlagGet);
  system("pause");
  return 0;
}
```
可以看到关键在V5这个变量，而这个变量在前面声明了，v5 = _mm_loadu_si128((const __m128i *)&xmmword_413E34);。
双击跳转回汇编区，找到xmmword_413E34值（有两段），按R键转换成字符串拼接，得到flag。
1. 稍微磨合了一下OD，发现如果会用的话这个可能也很方便。OD打开文件，汇编区右键->查找->所有参考文本字串，由于我们用IDA看过了整个流程，所以我们知道第一行的ASCII “%s”是获取flag对比时的文本。双击跳转回汇编，阅读汇编内容，这里涉及了eax ecx两个值，在差不多的地方下个硬件断点（右键->断点->硬件执行），保证能看到这两个值里面是什么内容就可以，点红色三角运行程序，输入任意值，到断点自动停止，查看寄存器中两个值的内容，得到flag。

#### 游戏过关
* 解题思路：想办法利用回环创造3个连在一起（1-8是个回环，8和1是串联的）的灭状态灯，再点亮。

#### easy_vb
* 解题思路：IDA打开，阅读汇编码，从` push    offset aMctfN3tRev1sE4 `找到flag。（一开始还以为这是个假flag，因为提示给的是flag{}，结果读了一遍hex没找到其它关键内容。正确flag就是把这个{}前面改成flag就好了）

#### Timer
* 解题思路：鉴于是个APK文件，开jadx-gui看一下MainActivity，找到关键部分：
```
            public void run() {
                MainActivity.this.t = System.currentTimeMillis();
                MainActivity.this.now = (int) (MainActivity.this.t / 1000);
                MainActivity.this.t = 1500 - (MainActivity.this.t % 1000);
                tv2.setText("AliCTF");
                if (MainActivity.this.beg - MainActivity.this.now <= 0) {
                    tv1.setText("The flag is:");
                    tv2.setText("alictf{" + MainActivity.this.stringFromJNI2(MainActivity.this.k) + "}");
                }
                if (MainActivity.is2(MainActivity.this.beg - MainActivity.this.now)) {
                    MainActivity.this.k += 100;
                } else {
                    MainActivity mainActivity = MainActivity.this;
                    mainActivity.k--;
                }
                tv1.setText("Time Remaining(s):" + (MainActivity.this.beg - MainActivity.this.now));
                handler.postDelayed(this, MainActivity.this.t);
            }
        }, 0);
    }
```
