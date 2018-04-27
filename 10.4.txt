#include<reg51.h>
#include"lcd.h"
#include"new_scan_key.c"
#define uchar unsigned char	  //宏定义
#define uint unsigned int
#define value_50ms 50000 
uchar key_value=0xff; 
uchar key_last,key_now;
uchar key_1,key_2,key_3;
uchar baiwei,shiwei,gewei;
uint set_ok_num; 
void int_t0();
void disp_input();
void disp_output();
void fenjie(uint);
void main()
{
  TMOD=0x01;
  TH0=(65536-value_50ms)/256;
  TH0=(65536-value_50ms)%256;
  TR0=1;
  EA=1;
  ET0=1;
  lcd_init();
  while(1)
  {
	   disp_input();
	   disp_output();
  }
}

void int_t0() interrupt 1
{
	  key_last=key_now;	  //把刚才的键值做更新
	  key_now=scan_key();
  if(key_last!=0xff&&key_now==0xff)//刚刚有按键按下又释放了
	{
		 if(key_last<10)  //如果当前按下是数字键
		 {
		   key_1=key_2;	//把第一位扔掉，用第二位更新为第一位
		   key_2=key_3;	//用第三位更新为第二位
		   key_3=key_last; //用最新按键值更新为第三位
		 }
	
		 else if(key_last==15) //如果当前按下是“确定”键
		 {
		   set_ok_num= key_1*100+key_2*10+key_3; //组合成数
           fenjie(set_ok_num);	 //分解得到个十百位
		 }	 	
	}
	  TH0=(65536-value_50ms)/256;  //重设定时器初值
	  TH0=(65536-value_50ms)%256;
}

void   disp_input()
{
  lcd_pos(1,1);
  lcdwrite_zm('i');
  lcdwrite_zm('n');
  lcdwrite_zm('p');
  lcdwrite_zm('u');
  lcdwrite_zm('t');
  lcdwrite_zm(':');
  lcdwrite_sz(key_1);
  lcdwrite_sz(key_2);
  lcdwrite_sz(key_3);
}

void disp_output()
{
  lcd_pos(2,1);
  lcdwrite_zm('o');
  lcdwrite_zm('u');
  lcdwrite_zm('t');
  lcdwrite_zm('p');
  lcdwrite_zm('u');
  lcdwrite_zm('t');
  lcdwrite_zm(':');
  lcdwrite_sz(baiwei);
  lcdwrite_sz(shiwei);
  lcdwrite_sz(gewei);
}

void fenjie(uint j)
{
  uchar remain;
  baiwei=j/100;
  remain=j%100;
  shiwei=remain/10;
  gewei=remain%10;
}
