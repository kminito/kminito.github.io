---
layout: post
title: "마작 짝 맞추기 봇 만들기 (1)"
category: python
tag: python
---


문제점
1. threshold

프렌즈 사천성의 경우 벽돌 블록, 붉은 색의 블록은 threshold로 인식하기 어려움
 -> 그것들이 있는 곳을 빈공간으로 생각하고 길을 찾기 때문에 제대로 작동하지 않음.
