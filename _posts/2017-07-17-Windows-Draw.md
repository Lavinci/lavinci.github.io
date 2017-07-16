---
layouts: post
title: "windows的窗口作图"
excerpt: "paint"
date: 2017-07-16 17:00
tags: [windows]
---


## 网格绘制
```c++
	case WM_PAINT:
		{
			HDC hdc;
			PAINTSTRUCT ps;
			RECT rt;
			int x,y;

			hdc=::BeginPaint(hwnd,&ps);
			::GetClientRect(hwnd,&rt);

			for(x=0;x<rt.right-rt.left;x+=50)
			{
				::MoveToEx(hdc,x,0,NULL);
				::LineTo(hdc,x,rt.bottom-rt.top);			
			}
			for(y=0;y<rt.bottom-rt.top;y+=50)
			{
				::MoveToEx(hdc,0,y,NULL);
				::LineTo(hdc,rt.right-rt.left,y);	
			}
	

			::EndPaint(hwnd,&ps);
			return 0;
		}

```
## SIN
```c++
	case WM_PAINT:
		{
#define SEGMENTS 500
#define PI 3.1415926
			HDC hdc;
			PAINTSTRUCT ps;
			RECT rt;
			int cxClient,cyClient;
			POINT pt[SEGMENTS];
			int i;
		
			hdc=::BeginPaint(hwnd,&ps);
			::GetClientRect(hwnd,&rt);
			cxClient=rt.right-rt.left;
			cyClient=rt.bottom-rt.top;

			::MoveToEx(hdc,0,cyClient/2,NULL);
			::LineTo(hdc,cxClient,cyClient/2);
		
			for(i=0;i<500;i++)
			{
				pt[i].x=cxClient*i/SEGMENTS;
				pt[i].y=(int)((cyClient/2)*(1-sin(2*PI*i/SEGMENTS)));
			
			}
			::Polyline(hdc,pt,SEGMENTS);
			::EndPaint(hwnd,&ps);
			break;
		}
```
