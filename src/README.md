# src

Keil C51 工程放这里。

## 建议结构

```
src/
├── stc51-car.uvprojx     # Keil 工程文件
├── main.c                # 主循环
├── board.h               # 引脚定义、常量（改引脚只改这里）
├── led.c / led.h         # 点灯
├── motor.c / motor.h     # PWM 调速
└── track.c / track.h     # 循迹传感器读取
```

## 约定

1. **引脚宏只写在 `board.h`**，其他文件一律引用宏，不写裸数字。
2. 每个模块一对 `.c/.h`，先跑通再合并。
3. 提交前删掉 `Objects/`、`Listings/`（`.gitignore` 已挡，但偶尔会漏）。
4. `.hex` 是产物，可以提交，方便换电脑直接下载。

## 第一个程序（M1 点灯）

思路：P1.0 输出方波，周期 1s（亮 500ms / 灭 500ms）。

```c
#include <reg52.h>

sbit LED = P1^0;

void delay_ms(unsigned int ms)
{
    unsigned int i, j;
    for (i = ms; i > 0; i--)
        for (j = 110; j > 0; j--);   /* 11.0592MHz 下约 1ms，需实测校准 */
}

void main(void)
{
    while (1) {
        LED = 0;          /* 低电平点亮（多数最小系统板） */
        delay_ms(500);
        LED = 1;
        delay_ms(500);
    }
}
```

如果 LED 常亮/常灭不闪，先怀疑：极性反了、P1.0 没接 LED、程序没下载进去（而不是先怀疑代码）。
