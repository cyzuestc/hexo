---
title: Scanner用法学习
date: 2017-07-09 09:42:02
tags:
---
```
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int x =0,y=0 ;
		int n = sc.nextInt();
		int xMax = Integer.MIN_VALUE;
		int yMax = Integer.MIN_VALUE;
		int xMin = Integer.MAX_VALUE;
		int yMin = Integer.MAX_VALUE;
		
		for (int i = 0; i < n; i++) {
			x = sc.nextInt();
			y = sc.nextInt();
			if(x>xMax)xMax = x;
			if(x<xMin)xMin = x;
			if(y>yMax)yMax = y;
			if(y<yMin)yMin = y;
		}
		int side = Math.max(xMax-xMin, yMax-yMin);
		System.out.println(side*side);
		
		
	}
```
