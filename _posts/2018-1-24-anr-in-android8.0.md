---
layout:     post
title:      "Android8.0上诡异的动画ANR问题"
subtitle:   " \"还有这种操作？\""
date:       2018-1-24 17:23:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
    - ANR
---

之前项目里用到了一种带动画的饼图控件，在以往的安卓系统中都能正常运行，最近发现在Android8.0上会突然导致整个程序ANR。（MIUI在发版前两天正好更新了同事的mix2到Android8.0，干得漂亮→_→）

关键代码：

    private class PieChartAnimation extends Animation {
        @Override
        protected void applyTransformation(float interpolatedTime, Transformation t) {
            super.applyTransformation(interpolatedTime, t);
            sweeps = new float[values.length];
            if (interpolatedTime <= 1.0f) {
                for (int i = 0; i < values.length; i++) {
                    sweeps[i] = (values[i] * interpolatedTime / totalValue) * 360;
                }
            } else {
                for (int i = 0; i < values.length; i++) {
                    sweeps[i] = (float) (values[i] / totalValue) * 360;
                }
            }
            invalidate();
        }
    }


一开始以为是动画在8.0不适配导致的，时间紧迫就先在8.0上禁用了动画直接加载结果。后面排查问题优先查了动画在8.0系统有什么变化，并没发现有什么异常。

百思不得其解时，看到怎么有个totalValue当除数分母没防0？随手加上了当totalValue为0时的判断和处理。一运行ANR居然没了O_O???

修改后的代码：

	private class PieChartAnimation extends Animation {
        @Override
        protected void applyTransformation(float interpolatedTime, Transformation t) {
            super.applyTransformation(interpolatedTime, t);
            sweeps = new float[values.length];
            if (interpolatedTime <= 1.0f) {
                for (int i = 0; i < values.length; i++) {
                    if (totalValue <= 0) {//加了这个if
                        sweeps[i] = 0;
                    } else {
                        sweeps[i] = (values[i] * interpolatedTime / totalValue) * 360;
                    }
                }
            } else {
                for (int i = 0; i < values.length; i++) {
                    if (totalValue <= 0) {//加了这个if
                        sweeps[i] = 0;
                    } else {
                        sweeps[i] = (float) (values[i] / totalValue) * 360;
                    }
                }
            }
            invalidate();
        }
    }

又涨姿势了Orz