---
layout: post
title: RecyclerView使用详解（二）
tags: [view]
categories: [android]
---

在上一篇（[RecyclerView使用详解（一）](http://frank-zhu.github.io/android/2015/01/16/android-recyclerview-part-1/)）文章中简单的介绍了RecyclerView的基本用法，接下来要来讲讲RecyclerView的更多用法，要实现不同的功能效果，大部分都还是在于RecyclerView的Adapter写法，所以我们着重来看看几种不同功能的Adapter写法。

##一、多Item布局实现(MultipleItem)

如果之前你用过ListView实现过此功能，那么你一定对下面这两个方法并不陌生

{% highlight ruby %}

	@Override
    public int getItemViewType(int position) {
        return super.getItemViewType(position);
    }

    @Override
    public int getViewTypeCount() {
        return super.getViewTypeCount();
    }

{% endhighlight %}

其中**getItemViewType**方法是用来获取当前项Item(position参数)是哪种类型的布局，**getViewTypeCount**方法是用来获取当前listview总共有多少种类型的布局。

如果你用RecyclerView，你会发现**getViewTypeCount**这个方法没有了，只有一个**getItemViewType**方法，用法和listview没有任何区别，这里要注意的就是这个函数**onCreateViewHolder(ViewGroup parent, int viewType)**这里的第二个参数就是View的类型，可以根据这个类型判断去创建不同item的ViewHolder。

{% highlight ruby %}
	public class MultipleItemAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    public static enum ITEM_TYPE {
        ITEM_TYPE_IMAGE,
        ITEM_TYPE_TEXT
    }
    
    private final LayoutInflater mLayoutInflater;
    private final Context mContext;
    private String[] mTitles;

    public MultipleItemAdapter(Context context) {
        mTitles = context.getResources().getStringArray(R.array.titles);
        mContext = context;
        mLayoutInflater = LayoutInflater.from(context);
    }

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if (viewType == ITEM_TYPE.ITEM_TYPE_IMAGE.ordinal()) {
            return new ImageViewHolder(mLayoutInflater.inflate(R.layout.item_image, parent, false));
        } else {
            return new TextViewHolder(mLayoutInflater.inflate(R.layout.item_text, parent, false));
        }
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        if (holder instanceof TextViewHolder) {
            ((TextViewHolder) holder).mTextView.setText(mTitles[position]);
        } else if (holder instanceof ImageViewHolder) {
            ((ImageViewHolder) holder).mTextView.setText(mTitles[position]);
        }
    }

    @Override
    public int getItemViewType(int position) {
        return position % 2 == 0 ? ITEM_TYPE.ITEM_TYPE_IMAGE.ordinal() : ITEM_TYPE.ITEM_TYPE_TEXT.ordinal();
    }

    @Override
    public int getItemCount() {
        return mTitles == null ? 0 : mTitles.length;
    }

    public static class TextViewHolder extends RecyclerView.ViewHolder {
        @InjectView(R.id.text_view)
        TextView mTextView;

        TextViewHolder(View view) {
            super(view);
            ButterKnife.inject(this, view);
            view.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Log.d("TextViewHolder", "onClick--> position = " + getPosition());
                }
            });
        }
    }

    public static class ImageViewHolder extends RecyclerView.ViewHolder {
        @InjectView(R.id.text_view)
        TextView mTextView;
        @InjectView(R.id.image_view)
        ImageView mImageView;

        ImageViewHolder(View view) {
            super(view);
            ButterKnife.inject(this, view);
            view.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Log.d("ImageViewHolder", "onClick--> position = " + getPosition());
                }
            });
        }
    }
	}
    
{% endhighlight %}

怎么样，是不是很简单，没错，就是这么简单，但是只要会了这个多Item，那么你就可以做出很多效果了，比如HeaderView和BottomView也是可以通过这种方式来实现的。([HeaderView Adapter](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo/blob/master/app/src/main/java/com/frankzhu/recyclerviewdemo/adapter/HeaderBottomItemAdapter.java))

##二、效果图如下：

![](https://raw.githubusercontent.com/Frank-Zhu/AndroidRecyclerViewDemo/master/art/multiple_item_1.png)

![](https://raw.githubusercontent.com/Frank-Zhu/AndroidRecyclerViewDemo/master/art/multiple_item_2.png)


最后给出代码下载地址-->[Demo Code](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo)

[RecyclerView使用详解（一）](http://frank-zhu.github.io/android/2015/01/16/android-recyclerview-part-1/)
