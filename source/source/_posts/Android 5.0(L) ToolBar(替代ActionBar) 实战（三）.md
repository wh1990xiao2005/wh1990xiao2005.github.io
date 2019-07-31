---
title: Android 5.0(L) ToolBar(替代ActionBar) 实战（三）
date: 2014-11-01 14:35:44
tags: Android
categories: 编程世界
---

《代码大全》一书中提到：当我们编程时，不要简单停留在“在一种编程语言上进行编程”，而是要“深入一种语言”编程。在前面两篇文章中，虽然初步实现了ViewPager+Tab的布局，但实际上还是有一些不足的。让我们把目光锁定在Tab：   
![Tab标签样式](https://img-blog.csdn.net/20141101131904159?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

可以发现，3个Tab加一起的宽度并没有填充屏幕整个宽度。实际上，Google这样的设计是为了应对多Tab的情形，当Tab总宽度超出屏幕宽度的时候，用户可以简单地通过滑动Tab看到后面未显示出来的Tab。这种情况其实还是比较多见的，比如众多的新闻阅读客户端和Google原生应用等等。   
但是如果我们只有三个Tab，甚至只有两个Tab，想让整个Tab的宽度等于屏幕宽度，该如何做呢？第一反应自然是要略微折腾一下SlidingTabLayout.java的源码了。   
经过研究发现，实际上如上图顶部Tab的实现通过HorizontalScrollView，然后add了一个又一个的view，这个View就是每个Tab的标题，而整个SlidingTabLayout是继承了HorizontalScrollView。   
接下来的思路就很清晰了：首先获取屏幕的总宽度，然后传给SlidingTabLayout对象，在其类内部对每个Tab所占的宽度进行计算和分配，最后将每个Tab标题的视图add到HorizontalScrollView就OK了。   
首先获取屏幕的宽度。这一操作我们在MainActivity中进行，下面列出这一段程序清单：   

```
// 获取屏幕宽度
private int caculateScreenX() {
	return getResources().getDisplayMetrics().widthPixels;
}
```

获取到屏幕宽度之后，接下来就是把这个值传递给SlidingTabLayout。回顾一下用法，上一篇文章里面提到，我们在添加Tab导航栏的时候，仅仅是实例化了SlidingTabLayout的对象，然后调用了setViewPager()方法而已。那么，我们不妨在该方法上面添加一个参数，用来传递屏幕宽度。当然，我们需要在SlidingTabLayout中声明一个全局的int变量，用来存放每个Tab的宽度值：   

```
public void setViewPager(ViewPager viewPager, int screenX) {
    this.screenX = screenX / viewPager.viewPager.getAdapter().getCount();
    mTabStrip.removeAllViews();
    mViewPager = viewPager;
    if (viewPager != null) {
        viewPager.setOnPageChangeListener(new InternalViewPagerListener());
        populateTabStrip();
    }
}
```

跟随该方法，找到populateTabStrip()方法，可以看到其中通过一个for循环以此添加了各个Tab视图。而每个Tab视图均通过createDefaultTabView()方法创建，因此该方法最为关键。  
找到这个方法之后我们发现，每个Tab的标题View实际上是TextView，那么我们只需将每个TextView的宽度制定为刚刚计算出来的screenX（每个Tab的宽度）即可。具体可参看下面的代码清单：   

```
protected TextView createDefaultTabView(Context context) {
    TextView textView = new TextView(context);
	textView.setGravity(Gravity.CENTER);
	textView.setTextSize(TypedValue.COMPLEX_UNIT_SP, TAB_VIEW_TEXT_SIZE_SP);
	textView.setTypeface(Typeface.DEFAULT_BOLD);		textView.setWidth(screenX);
 
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
		// If we're running on Honeycomb or newer, then we can use the
		// Theme's
		// selectableItemBackground to ensure that the View has a pressed
		// state
		TypedValue outValue = new TypedValue();
		getContext().getTheme().resolveAttribute(
					android.R.attr.selectableItemBackground, outValue, true);
		textView.setBackgroundResource(outValue.resourceId);
	}
 
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.ICE_CREAM_SANDWICH) {
		// If we're running on ICS or newer, enable all-caps to match the
		// Action Bar tab style
		textView.setAllCaps(true);
	}
 
	int padding = (int) (TAB_VIEW_PADDING_DIPS * getResources()
				.getDisplayMetrics().density);
	textView.setPadding(padding, padding, padding, padding);
 
	return textView;
}
```

最后，我们修改MainActivity，把计算好的屏幕宽度传给SlidingTabLayout，Ctrl+F11看看效果吧！   

![等分的Tab样式](https://img-blog.csdn.net/20141101143436171?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

不出意外的话，看到上图就说明已经成功了！   
下面放上整个SlidingTabLayout修改后的类：   

```
/*
 * Copyright (C) 2013 The Android Open Source Project
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
 
package com.xwh.toolbardemo.view;
 
import android.annotation.SuppressLint;
import android.content.Context;
import android.graphics.Typeface;
import android.os.Build;
import android.support.v4.view.PagerAdapter;
import android.support.v4.view.ViewPager;
import android.util.AttributeSet;
import android.util.Log;
import android.util.TypedValue;
import android.view.Gravity;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.HorizontalScrollView;
import android.widget.TextView;
 
/**
 * To be used with ViewPager to provide a tab indicator component which give
 * constant feedback as to the user's scroll progress.
 * <p>
 * To use the component, simply add it to your view hierarchy. Then in your
 * {@link android.app.Activity} or {@link android.support.v4.app.Fragment} call
 * {@link #setViewPager(ViewPager)} providing it the ViewPager this layout is
 * being used for.
 * <p>
 * The colors can be customized in two ways. The first and simplest is to
 * provide an array of colors via {@link #setSelectedIndicatorColors(int...)}
 * and {@link #setDividerColors(int...)}. The alternative is via the
 * {@link TabColorizer} interface which provides you complete control over which
 * color is used for any individual position.
 * <p>
 * The views used as tabs can be customized by calling
 * {@link #setCustomTabView(int, int)}, providing the layout ID of your custom
 * layout.
 */
public class SlidingTabLayout extends HorizontalScrollView {
 
	/**
	 * Allows complete control over the colors drawn in the tab layout. Set with
	 * {@link #setCustomTabColorizer(TabColorizer)}.
	 */
	public interface TabColorizer {
 
		/**
		 * @return return the color of the indicator used when {@code position}
		 *         is selected.
		 */
		int getIndicatorColor(int position);
 
		/**
		 * @return return the color of the divider drawn to the right of
		 *         {@code position}.
		 */
		int getDividerColor(int position);
 
	}
 
	private static final int TITLE_OFFSET_DIPS = 24;
	private static final int TAB_VIEW_PADDING_DIPS = 16;
	private static final int TAB_VIEW_TEXT_SIZE_SP = 12;
 
	private int mTitleOffset;
 
	private int mTabViewLayoutId;
	private int mTabViewTextViewId;
 
	private ViewPager mViewPager;
	private ViewPager.OnPageChangeListener mViewPagerPageChangeListener;
 
	private final SlidingTabStrip mTabStrip;
 
	private int screenX;
 
	public SlidingTabLayout(Context context) {
		this(context, null);
	}
 
	public SlidingTabLayout(Context context, AttributeSet attrs) {
		this(context, attrs, 0);
	}
 
	public SlidingTabLayout(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
 
		// Disable the Scroll Bar
		setHorizontalScrollBarEnabled(false);
		// Make sure that the Tab Strips fills this View
		setFillViewport(true);
 
		mTitleOffset = (int) (TITLE_OFFSET_DIPS * getResources()
				.getDisplayMetrics().density);
 
		mTabStrip = new SlidingTabStrip(context);
		addView(mTabStrip, LayoutParams.MATCH_PARENT, LayoutParams.WRAP_CONTENT);
	}
 
	/**
	 * Set the custom {@link TabColorizer} to be used.
	 * 
	 * If you only require simple custmisation then you can use
	 * {@link #setSelectedIndicatorColors(int...)} and
	 * {@link #setDividerColors(int...)} to achieve similar effects.
	 */
	public void setCustomTabColorizer(TabColorizer tabColorizer) {
		mTabStrip.setCustomTabColorizer(tabColorizer);
	}
 
	/**
	 * Sets the colors to be used for indicating the selected tab. These colors
	 * are treated as a circular array. Providing one color will mean that all
	 * tabs are indicated with the same color.
	 */
	public void setSelectedIndicatorColors(int... colors) {
		mTabStrip.setSelectedIndicatorColors(colors);
	}
 
	/**
	 * Sets the colors to be used for tab dividers. These colors are treated as
	 * a circular array. Providing one color will mean that all tabs are
	 * indicated with the same color.
	 */
	public void setDividerColors(int... colors) {
		mTabStrip.setDividerColors(colors);
	}
 
	/**
	 * Set the {@link ViewPager.OnPageChangeListener}. When using
	 * {@link SlidingTabLayout} you are required to set any
	 * {@link ViewPager.OnPageChangeListener} through this method. This is so
	 * that the layout can update it's scroll position correctly.
	 * 
	 * @see ViewPager#setOnPageChangeListener(ViewPager.OnPageChangeListener)
	 */
	public void setOnPageChangeListener(ViewPager.OnPageChangeListener listener) {
		mViewPagerPageChangeListener = listener;
	}
 
	/**
	 * Set the custom layout to be inflated for the tab views.
	 * 
	 * @param layoutResId
	 *            Layout id to be inflated
	 * @param textViewId
	 *            id of the {@link TextView} in the inflated view
	 */
	public void setCustomTabView(int layoutResId, int textViewId) {
		mTabViewLayoutId = layoutResId;
		mTabViewTextViewId = textViewId;
	}
 
	/**
	 * Sets the associated view pager. Note that the assumption here is that the
	 * pager content (number of tabs and tab titles) does not change after this
	 * call has been made.
	 */
	public void setViewPager(ViewPager viewPager, int screenX) {
		this.screenX = (screenX / viewPager.getAdapter().getCount());
		mTabStrip.removeAllViews();
 
		mViewPager = viewPager;
		if (viewPager != null) {
			viewPager.setOnPageChangeListener(new InternalViewPagerListener());
			populateTabStrip();
		}
	}
 
	/**
	 * Create a default view to be used for tabs. This is called if a custom tab
	 * view is not set via {@link #setCustomTabView(int, int)}.
	 */
	@SuppressLint("NewApi")
	protected TextView createDefaultTabView(Context context) {
		TextView textView = new TextView(context);
		textView.setGravity(Gravity.CENTER);
		textView.setTextSize(TypedValue.COMPLEX_UNIT_SP, TAB_VIEW_TEXT_SIZE_SP);
		textView.setTypeface(Typeface.DEFAULT_BOLD);
		textView.setWidth(screenX);
 
		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
			// If we're running on Honeycomb or newer, then we can use the
			// Theme's
			// selectableItemBackground to ensure that the View has a pressed
			// state
			TypedValue outValue = new TypedValue();
			getContext().getTheme().resolveAttribute(
					android.R.attr.selectableItemBackground, outValue, true);
			textView.setBackgroundResource(outValue.resourceId);
		}
 
		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.ICE_CREAM_SANDWICH) {
			// If we're running on ICS or newer, enable all-caps to match the
			// Action Bar tab style
			textView.setAllCaps(true);
		}
 
		int padding = (int) (TAB_VIEW_PADDING_DIPS * getResources()
				.getDisplayMetrics().density);
		textView.setPadding(padding, padding, padding, padding);
 
		return textView;
	}
 
	private void populateTabStrip() {
		final PagerAdapter adapter = mViewPager.getAdapter();
		final View.OnClickListener tabClickListener = new TabClickListener();
 
		for (int i = 0; i < adapter.getCount(); i++) {
			View tabView = null;
			TextView tabTitleView = null;
 
			if (mTabViewLayoutId != 0) {
				// If there is a custom tab view layout id set, try and inflate
				// it
				tabView = LayoutInflater.from(getContext()).inflate(
						mTabViewLayoutId, mTabStrip, false);
				tabTitleView = (TextView) tabView
						.findViewById(mTabViewTextViewId);
			}
 
			if (tabView == null) {
				tabView = createDefaultTabView(getContext());
			}
 
			if (tabTitleView == null && TextView.class.isInstance(tabView)) {
				tabTitleView = (TextView) tabView;
			}
 
			tabTitleView.setText(adapter.getPageTitle(i));
			tabView.setOnClickListener(tabClickListener);
 
			mTabStrip.addView(tabView);
		}
	}
 
	@Override
	protected void onAttachedToWindow() {
		super.onAttachedToWindow();
 
		if (mViewPager != null) {
			scrollToTab(mViewPager.getCurrentItem(), 0);
		}
	}
 
	private void scrollToTab(int tabIndex, int positionOffset) {
		final int tabStripChildCount = mTabStrip.getChildCount();
		if (tabStripChildCount == 0 || tabIndex < 0
				|| tabIndex >= tabStripChildCount) {
			return;
		}
 
		View selectedChild = mTabStrip.getChildAt(tabIndex);
		if (selectedChild != null) {
			int targetScrollX = selectedChild.getLeft() + positionOffset;
 
			if (tabIndex > 0 || positionOffset > 0) {
				// If we're not at the first child and are mid-scroll, make sure
				// we obey the offset
				targetScrollX -= mTitleOffset;
			}
 
			scrollTo(targetScrollX, 0);
		}
	}
 
	private class InternalViewPagerListener implements
			ViewPager.OnPageChangeListener {
		private int mScrollState;
 
		@Override
		public void onPageScrolled(int position, float positionOffset,
				int positionOffsetPixels) {
			int tabStripChildCount = mTabStrip.getChildCount();
			if ((tabStripChildCount == 0) || (position < 0)
					|| (position >= tabStripChildCount)) {
				return;
			}
 
			mTabStrip.onViewPagerPageChanged(position, positionOffset);
 
			View selectedTitle = mTabStrip.getChildAt(position);
			int extraOffset = (selectedTitle != null) ? (int) (positionOffset * selectedTitle
					.getWidth()) : 0;
			scrollToTab(position, extraOffset);
 
			if (mViewPagerPageChangeListener != null) {
				mViewPagerPageChangeListener.onPageScrolled(position,
						positionOffset, positionOffsetPixels);
			}
		}
 
		@Override
		public void onPageScrollStateChanged(int state) {
			mScrollState = state;
 
			if (mViewPagerPageChangeListener != null) {
				mViewPagerPageChangeListener.onPageScrollStateChanged(state);
			}
		}
 
		@Override
		public void onPageSelected(int position) {
			if (mScrollState == ViewPager.SCROLL_STATE_IDLE) {
				mTabStrip.onViewPagerPageChanged(position, 0f);
				scrollToTab(position, 0);
			}
 
			if (mViewPagerPageChangeListener != null) {
				mViewPagerPageChangeListener.onPageSelected(position);
			}
		}
 
	}
 
	private class TabClickListener implements View.OnClickListener {
		@Override
		public void onClick(View v) {
			for (int i = 0; i < mTabStrip.getChildCount(); i++) {
				if (v == mTabStrip.getChildAt(i)) {
					mViewPager.setCurrentItem(i);
					return;
				}
			}
		}
	}
 
}
```

这一篇到此结束。下一篇将讨论菜单的添加和关于ToolBar的更多精彩用法。