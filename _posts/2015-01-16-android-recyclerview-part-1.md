---
layout: post
title: RecyclerView使用详解（一）
tags: [view]
categories: [android]
---
##一、前言
RecyclerView是谷歌V7包下新增的控件,用来替代ListView的使用,在RecyclerView标准化了ViewHolder类似于ListView中convertView用来做视图缓.

先来说说RecyclerView的有点就是,他可以通过设置LayoutManager来快速实现listview、gridview、瀑布流的效果，而且还可以设置横向和纵向显示，添加动画效果也非常简单(自带了ItemAnimation，可以设置加载和移除时的动画，方便做出各种动态浏览的效果),也是官方推荐使用的.以下是官方的说明:

>RecyclerView is a more advanced and flexible version of ListView. This widget is a container for large sets of views that can be recycled and scrolled very efficiently. Use the RecyclerView widget when you have lists with elements that change dynamically.
>简单说就是当你需要动态展示一组数据的时候就会需要用到它。

讲了这么多虚的，接下来我们看看在代码中怎么来实现.

##二、实现

首先要用这个控件，你需要在gradle文件中添加包的引用（配合官方CardView使用）

{% highlight ruby %}

compile 'com.android.support:cardview-v7:21.0.3'
compile 'com.android.support:recyclerview-v7:21.0.3'

{% endhighlight %}

然后是在XML文件用使用它

{% highlight ruby %}
<android.support.v7.widget.RecyclerView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/recycler_view"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"/>
    
{% endhighlight %}

接着在Activity中设置它

{% highlight ruby %}

public class MainActivity extends ActionBarActivity {
    @InjectView(R.id.recycler_view)
    RecyclerView mRecyclerView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.inject(this);

        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));//这里用线性显示 类似于listview
//        mRecyclerView.setLayoutManager(new GridLayoutManager(this, 2));//这里用线性宫格显示 类似于grid view
//        mRecyclerView.setLayoutManager(new StaggeredGridLayoutManager(2, OrientationHelper.VERTICAL));//这里用线性宫格显示 类似于瀑布流
        mRecyclerView.setAdapter(new NormalRecyclerViewAdapter(this));
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();

        if (id == R.id.action_settings) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }
}

{% endhighlight %}

然后是适配器代码
{% highlight ruby %}

public class NormalRecyclerViewAdapter extends RecyclerView.Adapter<NormalRecyclerViewAdapter.NormalTextViewHolder> {
    private final LayoutInflater mLayoutInflater;
    private final Context mContext;
    private String[] mTitles;

    public NormalRecyclerViewAdapter(Context context) {
        mTitles = context.getResources().getStringArray(R.array.titles);
        mContext = context;
        mLayoutInflater = LayoutInflater.from(context);
    }

    @Override
    public NormalTextViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        return new NormalTextViewHolder(mLayoutInflater.inflate(R.layout.item_text, parent, false));
    }

    @Override
    public void onBindViewHolder(NormalTextViewHolder holder, int position) {
        holder.mTextView.setText(mTitles[position]);
    }

    @Override
    public int getItemCount() {
        return mTitles == null ? 0 : mTitles.length;
    }

    public static class NormalTextViewHolder extends RecyclerView.ViewHolder {
        @InjectView(R.id.text_view)
        TextView mTextView;

        NormalTextViewHolder(View view) {
            super(view);
            ButterKnife.inject(this, view);
            view.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Log.d("NormalTextViewHolder", "onClick--> position = " + getPosition());
                }
            });
        }
    }
}

{% endhighlight %}

##三、效果图


LinearLayoutManager样式

![](https://raw.githubusercontent.com/Frank-Zhu/AndroidRecyclerViewDemo/master/art/normal_2.png)

GridLayoutManager样式

![](https://raw.githubusercontent.com/Frank-Zhu/AndroidRecyclerViewDemo/master/art/normal_1.png)


最后给出代码下载地址-->[Demo Code](https://github.com/Frank-Zhu/AndroidRecyclerViewDemo)

[RecyclerView使用详解（二）](http://frank-zhu.github.io/android/2015/02/25/android-recyclerview-part-2/)

[RecyclerView使用详解（三）](http://frank-zhu.github.io/android/2015/02/26/android-recyclerview-part-3/)

