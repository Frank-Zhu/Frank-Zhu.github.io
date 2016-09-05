---
layout: post
title: RecyclerView使用详解（三）
tags: [view]
categories: [android]
---

在上一篇（[RecyclerView使用详解（二）](http://frank-zhu.github.io/android/2015/02/25/android-recyclerview-part-2/)）文章中介绍了RecyclerView的多Item布局实现，接下来要来讲讲RecyclerView的Cursor实现，相较于之前的实现，Cursor有更多的使用场景，也更加的常用，特别是配合LoaderManager和CursorLoader进行数据的缓存及加载显示，基于此我们来重点看看RecyclerView的CursorAdapter具体要怎么实现。

## 一、CursorAdapter实现(配合LoaderManager和CursorLoader)

如果之前你用过ListView实现过此功能(CursorAdapter)，那么你一定对下面这两个方法并不陌生

{% highlight ruby %}

	@Override
    public View newView(Context context, Cursor cursor, ViewGroup parent) {
        return null;
    }

    @Override
    public void bindView(View view, Context context, Cursor cursor) {

    }

{% endhighlight %}

其中**newView**方法是用来创建Item布局的，**bindView** 方法是用来绑定当前View数据的，就相当于之前的**getView**方法拆成了两个方法实现。

如果你用RecyclerView，你会发现**CursorAdapter**这个类没有了，既然没有了，那我们就自己仿照着ListView的[CursorAdapter](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/java/android/widget/CursorAdapter.java)类来实现，具体的代码没什么大的出入，无非就是注册两个观察者去监听数据库数据的变化，但是有两个地方需要注意一下，一个就是**hasStableIds()** 这个方法RecyclerView.Adapter中不能复写父类的方法，需要在初始化的时候调用**setHasStableIds(true);** 来完成相同功能，第二个就是**notifyDataSetInvalidated()** 这个方法没有，统一修改成**notifyDataSetChanged()** 方法即可。

{% highlight ruby %}
	void init(Context context, Cursor c, int flags) {
        boolean cursorPresent = c != null;
        mCursor = c;
        mDataValid = cursorPresent;
        mContext = context;
        mRowIDColumn = cursorPresent ? c.getColumnIndexOrThrow("_id") : -1;
        if ((flags & FLAG_REGISTER_CONTENT_OBSERVER) == FLAG_REGISTER_CONTENT_OBSERVER) {
            mChangeObserver = new ChangeObserver();
            mDataSetObserver = new MyDataSetObserver();
        } else {
            mChangeObserver = null;
            mDataSetObserver = null;
        }

        if (cursorPresent) {
            if (mChangeObserver != null) c.registerContentObserver(mChangeObserver);
            if (mDataSetObserver != null) c.registerDataSetObserver(mDataSetObserver);
        }

        setHasStableIds(true);//这个地方要注意一下，需要将表关联ID设置为true
    }

	//************//
	public Cursor swapCursor(Cursor newCursor) {
        if (newCursor == mCursor) {
            return null;
        }
        Cursor oldCursor = mCursor;
        if (oldCursor != null) {
            if (mChangeObserver != null) oldCursor.unregisterContentObserver(mChangeObserver);
            if (mDataSetObserver != null) oldCursor.unregisterDataSetObserver(mDataSetObserver);
        }
        mCursor = newCursor;
        if (newCursor != null) {
            if (mChangeObserver != null) newCursor.registerContentObserver(mChangeObserver);
            if (mDataSetObserver != null) newCursor.registerDataSetObserver(mDataSetObserver);
            mRowIDColumn = newCursor.getColumnIndexOrThrow("_id");
            mDataValid = true;
            // notify the observers about the new cursor
            notifyDataSetChanged();
        } else {
            mRowIDColumn = -1;
            mDataValid = false;
            // notify the observers about the lack of a data set
            //There is no notifyDataSetInvalidated() method in RecyclerView.Adapter
            notifyDataSetChanged();//注意此处
        }
        return oldCursor;
    }
	//************//
	private class MyDataSetObserver extends DataSetObserver {
        @Override
        public void onChanged() {
            mDataValid = true;
            notifyDataSetChanged();
        }

        @Override
        public void onInvalidated() {
            mDataValid = false;
            //There is no notifyDataSetInvalidated() method in RecyclerView.Adapter
            notifyDataSetChanged();//注意此处
        }
    }
    
{% endhighlight %}

怎么样，是不是很简单，没错，就是这么简单，这里是完整的[BaseAbstractRecycleCursorAdapter](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo/blob/master/app/src/main/java/com/frankzhu/recyclerviewdemo/adapter/BaseAbstractRecycleCursorAdapter.java)代码,用法和ListView的CursorAdapter用法一致，具体的可以看看我的[Recyclerview LoaderManager Provider](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo/blob/master/app/src/main/java/com/frankzhu/recyclerviewdemo/fragment/ItemsFragment.java)

## 二、Item的动画实现
RecyclerView本身就已经实现了ITEM的动画，只需要调用以下几个函数来增删Item即可出现默认动画。

{% highlight ruby %}

     	notifyItemChanged(int)
     notifyItemInserted(int)
     notifyItemRemoved(int)
     notifyItemRangeChanged(int, int)
     notifyItemRangeInserted(int, int)
     notifyItemRangeRemoved(int, int)

{% endhighlight %}

怎么样，是不是很轻松，如果你不满足系统默认动画，那么你可以自定义实现**RecyclerView.ItemAnimator**的接口方法，实现代码可以参考[DefaultItemAnimator](https://android.googlesource.com/platform/frameworks/support/+/refs/heads/master/v7/recyclerview/src/android/support/v7/widget/DefaultItemAnimator.java).当然，如果你不想自己实现，那么也没关系，这里有人已经写了开源库，你可以去看看[recyclerview-animators](https://github.com/wasabeef/recyclerview-animators),这里给出默认动画实现方式代码[AnimFragment](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo/blob/master/app/src/main/java/com/frankzhu/recyclerviewdemo/fragment/AnimFragment.java)

## 三、嵌套RecycleView

一般是不推荐使用嵌套RecycleView的，和ListView是类似的，遇到这种需要嵌套的View一般都是想别的办法来规避，比如动态AddView，或者通过RecycleView的[MultipleItemAdapter](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo/blob/master/app/src/main/java/com/frankzhu/recyclerviewdemo/adapter/MultipleItemAdapter.java)来实现，通过设置不同的ItemType布局不同的View，但是有时候会闲麻烦，想直接就用嵌套的方式来做，那么和ListView实现方式不同的是，ListView的实现一般都是继承ListView然后复写**onMeasure**方法，如下所示：

{% highlight ruby %}

	@Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2, MeasureSpec.AT_MOST);
        super.onMeasure(widthMeasureSpec, expandSpec);
    }

{% endhighlight %}

但是RecycleView的实现方式不再是继承RecycleView来做，而是通过修改LayoutManager的方式，即通过继承**LinearLayoutManager** **GridLayoutManager** **StaggeredGridLayoutManager**来修改子控件的测量，下面给出主要代码：

[FullyLinearLayoutManager](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo/blob/master/app/src/main/java/com/frankzhu/recyclerviewdemo/FullyLinearLayoutManager.java)

{% highlight ruby %}

	private int[] mMeasuredDimension = new int[2];

    @Override
    public void onMeasure(RecyclerView.Recycler recycler, RecyclerView.State state,
                          int widthSpec, int heightSpec) {

        final int widthMode = View.MeasureSpec.getMode(widthSpec);
        final int heightMode = View.MeasureSpec.getMode(heightSpec);
        final int widthSize = View.MeasureSpec.getSize(widthSpec);
        final int heightSize = View.MeasureSpec.getSize(heightSpec);

        Log.i(TAG, "onMeasure called. \nwidthMode " + widthMode
                + " \nheightMode " + heightSpec
                + " \nwidthSize " + widthSize
                + " \nheightSize " + heightSize
                + " \ngetItemCount() " + getItemCount());

        int width = 0;
        int height = 0;
        for (int i = 0; i < getItemCount(); i++) {
            measureScrapChild(recycler, i,
                    View.MeasureSpec.makeMeasureSpec(i, View.MeasureSpec.UNSPECIFIED),
                    View.MeasureSpec.makeMeasureSpec(i, View.MeasureSpec.UNSPECIFIED),
                    mMeasuredDimension);

            if (getOrientation() == HORIZONTAL) {
                width = width + mMeasuredDimension[0];
                if (i == 0) {
                    height = mMeasuredDimension[1];
                }
            } else {
                height = height + mMeasuredDimension[1];
                if (i == 0) {
                    width = mMeasuredDimension[0];
                }
            }
        }
        switch (widthMode) {
            case View.MeasureSpec.EXACTLY:
                width = widthSize;
            case View.MeasureSpec.AT_MOST:
            case View.MeasureSpec.UNSPECIFIED:
        }

        switch (heightMode) {
            case View.MeasureSpec.EXACTLY:
                height = heightSize;
            case View.MeasureSpec.AT_MOST:
            case View.MeasureSpec.UNSPECIFIED:
        }

        setMeasuredDimension(width, height);
    }

    private void measureScrapChild(RecyclerView.Recycler recycler, int position, int widthSpec,
                                   int heightSpec, int[] measuredDimension) {
        try {
            View view = recycler.getViewForPosition(0);//fix 动态添加时报IndexOutOfBoundsException

            if (view != null) {
                RecyclerView.LayoutParams p = (RecyclerView.LayoutParams) view.getLayoutParams();

                int childWidthSpec = ViewGroup.getChildMeasureSpec(widthSpec,
                        getPaddingLeft() + getPaddingRight(), p.width);

                int childHeightSpec = ViewGroup.getChildMeasureSpec(heightSpec,
                        getPaddingTop() + getPaddingBottom(), p.height);

                view.measure(childWidthSpec, childHeightSpec);
                measuredDimension[0] = view.getMeasuredWidth() + p.leftMargin + p.rightMargin;
                measuredDimension[1] = view.getMeasuredHeight() + p.bottomMargin + p.topMargin;
                recycler.recycleView(view);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
        }
    }

{% endhighlight %}


[FullyGridLayoutManager](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo/blob/master/app/src/main/java/com/frankzhu/recyclerviewdemo/FullyGridLayoutManager.java)

{% highlight ruby %}

	private int[] mMeasuredDimension = new int[2];

    @Override
    public void onMeasure(RecyclerView.Recycler recycler, RecyclerView.State state, int widthSpec, int heightSpec) {
        final int widthMode = View.MeasureSpec.getMode(widthSpec);
        final int heightMode = View.MeasureSpec.getMode(heightSpec);
        final int widthSize = View.MeasureSpec.getSize(widthSpec);
        final int heightSize = View.MeasureSpec.getSize(heightSpec);

        int width = 0;
        int height = 0;
        int count = getItemCount();
        int span = getSpanCount();
        for (int i = 0; i < count; i++) {
            measureScrapChild(recycler, i,
                    View.MeasureSpec.makeMeasureSpec(i, View.MeasureSpec.UNSPECIFIED),
                    View.MeasureSpec.makeMeasureSpec(i, View.MeasureSpec.UNSPECIFIED),
                    mMeasuredDimension);

            if (getOrientation() == HORIZONTAL) {
                if (i % span == 0) {
                    width = width + mMeasuredDimension[0];
                }
                if (i == 0) {
                    height = mMeasuredDimension[1];
                }
            } else {
                if (i % span == 0) {
                    height = height + mMeasuredDimension[1];
                }
                if (i == 0) {
                    width = mMeasuredDimension[0];
                }
            }
        }

        switch (widthMode) {
            case View.MeasureSpec.EXACTLY:
                width = widthSize;
            case View.MeasureSpec.AT_MOST:
            case View.MeasureSpec.UNSPECIFIED:
        }

        switch (heightMode) {
            case View.MeasureSpec.EXACTLY:
                height = heightSize;
            case View.MeasureSpec.AT_MOST:
            case View.MeasureSpec.UNSPECIFIED:
        }

        setMeasuredDimension(width, height);
    }

    private void measureScrapChild(RecyclerView.Recycler recycler, int position, int widthSpec,
                                   int heightSpec, int[] measuredDimension) {
        if (position < getItemCount()) {
            try {
                View view = recycler.getViewForPosition(0);//fix 动态添加时报IndexOutOfBoundsException
                if (view != null) {
                    RecyclerView.LayoutParams p = (RecyclerView.LayoutParams) view.getLayoutParams();
                    int childWidthSpec = ViewGroup.getChildMeasureSpec(widthSpec,
                            getPaddingLeft() + getPaddingRight(), p.width);
                    int childHeightSpec = ViewGroup.getChildMeasureSpec(heightSpec,
                            getPaddingTop() + getPaddingBottom(), p.height);
                    view.measure(childWidthSpec, childHeightSpec);
                    measuredDimension[0] = view.getMeasuredWidth() + p.leftMargin + p.rightMargin;
                    measuredDimension[1] = view.getMeasuredHeight() + p.bottomMargin + p.topMargin;
                    recycler.recycleView(view);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

{% endhighlight %}

## 四、效果图如下：

Item默认动画效果

![](https://raw.githubusercontent.com/Frank-Zhu/AndroidRecyclerViewDemo/master/art/anim_1.gif)

嵌套ScrollView效果

![](https://raw.githubusercontent.com/Frank-Zhu/AndroidRecyclerViewDemo/master/art/fully_expanded_1.gif)

最后给出代码下载地址-->[Demo Code](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo)

[RecyclerView使用详解（一）](http://frank-zhu.github.io/android/2015/01/16/android-recyclerview-part-1/)

[RecyclerView使用详解（二）](http://frank-zhu.github.io/android/2015/02/25/android-recyclerview-part-2/)
