---
title: 自定义ImageView系列 － 区域截图（下）
date: 2015-11-06 10:42:26
tags: Android
categories: 编程世界
---

## 功能要点：
 - 根据控件自身大小计算合适的透明正方形预览区；
 - 截取预览区图像并按照指定的尺寸缩放，生成Bitmap对象。

本文着重介绍上述第2个要点，涉及两个问题：

 - 屏幕指定区域截图；
 - 图片缩放。

由于在前文中，我们知道了指定区域的方位，那么解决起来就轻松很多了。只要先截取整个屏幕，然后利用Matrix将图片进行裁剪即可。  
截取到指定部分的图像后，再利用Matrix的postScale()方法即可进行缩放。  
下面放上代码片段：  

```
public Bitmap getAdjustedBitmap(Activity activity) {
    View view = activity.getWindow().getDecorView();
    view.setDrawingCacheEnabled(true);
    view.buildDrawingCache();
    bitmap = view.getDrawingCache();
    matrix = new Matrix();
    matrix.postScale((CommonSettings.AVATOR\_SIZE * 1.0f) / (previewEdge[1] - previewEdge[0]), (CommonSettings.AVATOR\_SIZE * 1.0f) / (previewEdge[1] - previewEdge[0]));
    return Bitmap.createBitmap(bitmap, previewEdge[0] - 2, previewEdge[2] + myLocationStart[1] - 2, previewEdge[1] - previewEdge[0], previewEdge[1] - previewEdge[0], matrix, false);
}
``` 

如上所示，即可完成截图和缩放操作。  
接下来放上整个重写的ImageView类的代码，这里面还包含了双指缩放和单指移动操作功能。  

```
import android.app.Activity;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Matrix;
import android.graphics.Paint;
import android.util.AttributeSet;
import android.view.MotionEvent;
import android.view.View;
import android.widget.ImageView;

public class PhotoChooseForAvatorImageView extends ImageView {
    private Bitmap bitmap;
    private Matrix matrix;
    private float point1\_old\_x, point1\_old\_y, point1\_new_x, point1\_new\_y,
            point2\_old\_x, point2\_old\_y, point2\_new\_x, point2\_new\_y,
            distance\_old, distance\_new;
    private float point\_old\_x, point\_old\_y, point\_new\_x, point\_new\_y;
    private boolean isMultiTouch = false;
    private int[] myLocationStart;
    private int[] myLocationEnd;
    private int[] myLocationMid;
    private int[] previewEdge;
    private int bitmapLocXStart, bitmapLocXEnd, bitmapLocYStart, bitmapLocYEnd;
    private Paint rectPaint;
    private Paint rectBgPaint;
    public PhotoChooseForAvatorImageView(Context context) {
        super(context);
        if (matrix == null) {
            matrix = new Matrix();
        }
    }
    public PhotoChooseForAvatorImageView(Context context, AttributeSet attrs) {
        super(context, attrs);
        if (matrix == null) {
            matrix = new Matrix();
        }
    }
    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.save();
        if (bitmap != null) {
            canvas.drawBitmap(bitmap, matrix, null);
        }
        if (previewEdge != null) {
            //上
            canvas.drawLine(previewEdge[0], previewEdge[2], previewEdge[1], previewEdge[2], rectPaint);
            //左
            canvas.drawLine(previewEdge[0], previewEdge[2], previewEdge[0], previewEdge[3], rectPaint);
            //下
            canvas.drawLine(previewEdge[0], previewEdge[3], previewEdge[1], previewEdge[3], rectPaint);
            //右
            canvas.drawLine(previewEdge[1], previewEdge[2], previewEdge[1], previewEdge[3], rectPaint);
            //灰色部分
            canvas.drawRect(0, 0, previewEdge[0], myLocationEnd[1], rectBgPaint);
            canvas.drawRect(previewEdge[0], 0, previewEdge[1], previewEdge[2], rectBgPaint);
            canvas.drawRect(previewEdge[1], 0, myLocationEnd[0], myLocationEnd[1], rectBgPaint);
            canvas.drawRect(previewEdge[0], previewEdge[3], previewEdge[1], myLocationEnd[1], rectBgPaint);
        }
        canvas.restore();
    }
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        switch (event.getAction() & MotionEvent.ACTION\_MASK) {
            case MotionEvent.ACTION\_DOWN:
                // 单点移动 按下
                if (event.getPointerCount() == 1) {
                    isMultiTouch = false;
                    point\_old\_x = event.getX();
                    point\_old\_y = event.getY();
                }
                break;
            case MotionEvent.ACTION\_UP:
                // 单点移动 抬起
                if (event.getPointerCount() == 1) {
                    isMultiTouch = false;
                    point\_old\_x = point\_new\_x;
                    point\_old\_y = point\_new\_y;
                }
                break;
            case MotionEvent.ACTION\_POINTER\_UP:
                // 双点操作 抬起
                if (event.getPointerCount() == 2) {
                    isMultiTouch = true;
                    point1\_old\_x = point1\_new\_x;
                    point1\_old\_y = point1\_new\_y;
                    point2\_old\_x = point2\_new\_x;
                    point2\_old\_y = point2\_new\_y;
                }
                break;
            case MotionEvent.ACTION\_POINTER\_DOWN:
                // 双点操作 按下
                if (event.getPointerCount() == 2) {
                    isMultiTouch = true;
                    point1\_old\_x = event.getX(0);
                    point1\_old\_y = event.getY(0);
                    point2\_old\_x = event.getX(1);
                    point2\_old\_y = event.getY(1);
                    distance_old = (float) Math
                            .sqrt(((point1\_old\_x - point2\_old\_x)
                                    * (point1\_old\_x - point2\_old\_x) + (point1\_old\_y - point2\_old\_y)
                                    * (point1\_old\_y - point2\_old\_y)));
                }
                break;
            case MotionEvent.ACTION_MOVE:
                // 双点操作 移动
                if (event.getPointerCount() == 2) {
                    isMultiTouch = true;
                    point1\_new\_x = event.getX(0);
                    point1\_new\_y = event.getY(0);
                    point2\_new\_x = event.getX(1);
                    point2\_new\_y = event.getY(1);
                    pinch();
                } else {
                    // 单点移动 移动
                    if (event.getPointerCount() == 1) {
                        if (!isMultiTouch) {
                            point_\new\_x = event.getX();
                            point\_new\_y = event.getY();
                            matrix.postTranslate((point\_new_x - point\_old\_x),
                                    (point\_new\_y - point\_old\_y));
                            point\_old\_x = point\_new\_x;
                            point\_old\_y = point\_new\_y;
                            invalidate();
                        }
                    }
                }
                break;
        }
        return true;
    }
    // 缩放图片方法
    private void pinch() {
        distance_new = (float) Math.sqrt(((point1_new_x - point2_new_x)
                * (point1_new_x - point2_new_x) + (point1_new_y - point2_new_y)
                * (point1_new_y - point2_new_y)));
        matrix.postScale((distance_new / distance_old),
                (distance_new / distance_old), this.getWidth() / 2,
                this.getTop() / 2);
        invalidate();
        distance_old = distance_new;
    }
    /**
     * 设置Bitmap
     *
     * @param bitmap
     */
    public void setImage(Bitmap bitmap) {
        this.bitmap = bitmap;
        //获取控件自身起始点
        myLocationStart = new int[2];
        getLocationOnScreen(myLocationStart);
        //获取控件自身终止点
        myLocationEnd = new int[2];
        myLocationEnd[0] = getWidth() + myLocationStart[0];
        myLocationEnd[1] = getHeight() + myLocationStart[1];
        //计算控件自身中点
        myLocationMid = new int[2];
        myLocationMid[0] = (myLocationStart[0] + myLocationEnd[0]) / 2;
        myLocationMid[1] = (myLocationStart[1] + myLocationEnd[1]) / 2 - myLocationStart[1];
        //设置中间预览框边框色
        rectPaint = new Paint();
        rectPaint.setColor(Color.WHITE);
        rectPaint.setStrokeWidth(2);
        rectBgPaint = new Paint();
        rectBgPaint.setColor(getResources().getColor(R.color.photo_adjust_for_avator_bg));
        //计算中间预览边框位置
        previewEdge = new int[4];
        previewEdge[0] = (myLocationMid[0] - myLocationStart[0]) / 8;
        previewEdge[1] = myLocationEnd[0] - previewEdge[0];
        previewEdge[2] = myLocationMid[1] - ((previewEdge[1] - previewEdge[0]) / 2);
        previewEdge[3] = myLocationMid[1] + ((previewEdge[1] - previewEdge[0]) / 2);
        //计算Bitmap起点
        bitmapLocXStart = myLocationMid[0] - bitmap.getWidth() / 2;
        bitmapLocYStart = myLocationMid[1] - bitmap.getHeight() / 2;
        bitmapLocXEnd = bitmapLocXStart + bitmap.getWidth();
        bitmapLocYEnd = bitmapLocYStart - bitmap.getHeight();
        //调整Bitmap位置
        matrix.postTranslate(bitmapLocXStart, bitmapLocYStart);
        PhotoChooseForAvatorImageView.this.invalidate();
    }
    /**
     * 截图并返回Bitmap对象
     */
    public Bitmap getAdjustedBitmap(Activity activity) {
        View view = activity.getWindow().getDecorView();
        view.setDrawingCacheEnabled(true);
        view.buildDrawingCache();
        bitmap = view.getDrawingCache();
        matrix = new Matrix();
        matrix.postScale((CommonSettings.AVATOR_SIZE * 1.0f) / (previewEdge[1] - previewEdge[0]), (CommonSettings.AVATOR_SIZE * 1.0f) / (previewEdge[1] - previewEdge[0]));
        return Bitmap.createBitmap(bitmap, previewEdge[0] - 2, previewEdge[2] + myLocationStart[1] - 2, previewEdge[1] - previewEdge[0], previewEdge[1] - previewEdge[0], matrix, false);
    }
    /**
     * 重置Bitmap（回收内存）
     */
    public void resetBitmap() {
        bitmap.recycle();
        bitmap = null;
    }
}
```