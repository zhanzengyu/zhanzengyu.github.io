---
title: RecyclerView进阶
---
![](http://upload-images.jianshu.io/upload_images/5361063-31be3bd1342e11c7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上篇文章我们讲解了RecyclerView的基本使用，本篇就让我们承接上篇文章讲下**RecyclerView的进阶使用**。

没看过上篇的朋友可以点击右边的传送门：[《RecyclerView基本使用》](http://www.jianshu.com/p/d8c31dce7e9f)

### 背景
RecyclerView有很多功能待发掘，刚好自己在工作过程中有遇到，因此抱着知识共享和互相学习的态度分享给大家。

本篇会讲3个RecyclerView的进阶使用。


##### 01.点击监听

大家知道，我们以前使用ListView的时候，ListView有条目点击回调方法setOnItemClickListener。

但是用过RecyclerView的会发现，

**竟然没有  
竟然没有  
竟然没有**  
![](http://upload-images.jianshu.io/upload_images/5361063-d83eac807c2f5432.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


是的，真的没有这个方法。

但是如果我真的要实现点击回调，怎么办呢？

这就是这里我们要说的啦。
我们可以通过在Adapter里面提供一个接口来达到类似的效果。

这里的Adapter为ItemClickRecyclerViewAdapter。

1.定义接口

```
public interface OnItemClickListener {
        void onItemClick(View view, int position);
}
```

2.定义该接口的变量并提供set方法以便回调回去。

```
private OnItemClickListener mOnItemClickListener = null;

public void setOnItemClickListener(OnItemClickListener mOnItemClickListener) {
        this.mOnItemClickListener = mOnItemClickListener;
}
```

3.在ItemClickRecyclerViewAdapter的***onCreateViewHolder()***方法里面对view设置点击监听。

```
@Override
public ItemClickRecyclerViewViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_main, parent, false);
    view.setOnClickListener(this);
    return new ItemClickRecyclerViewViewHolder(view);
}
```

**NOTE**:
为了把点击的位置回调回去，还需要在***onBindViewHolder()***方法做如下处理：

```
holder.itemView.setTag(position);
```

4.让ItemClickRecyclerViewAdapter实现监听并做回调处理。

```
@Override
public void onClick(View view) {
    if (mOnItemClickListener != null) {
        mOnItemClickListener.onItemClick(view, (int) view.getTag());
    }
}
```

如此，点击监听就实现了。

##### 02.互换位置
先上图：  
![](http://upload-images.jianshu.io/upload_images/5361063-6fb800c7a4b2d8e2.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

好了，让我们看看如何实现上图的效果吧。

1.定义类DragItemTouchHelperCallback继承**ItemTouchHelper.Callback**。

2.定义一个接口用于上下拖动时交换位置。

```
public interface OnItemCallbackListener {
    /**
     * @param fromPosition 起始位置
     * @param toPosition   移动的位置
     */
    void onMove(int fromPosition, int toPosition);
}
```

3.DragItemTouchHelperCallback定义**OnItemCallbackListener**变量并作为参数通过构造函数传递进来。

```
private OnItemCallbackListener mOnItemCallbackListener;

public DragItemTouchHelperCallback(OnItemCallbackListener mOnItemCallbackListener) {
    this.mOnItemCallbackListener = mOnItemCallbackListener;
}
```

4.修改***getMovementFlags()***方法设置为可上下拖拽。

```
@Override
public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
    //设置为可上下拖拽
    int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
    return makeMovementFlags(dragFlags, 0);
}
```

5.修改***onMove()***方法提供回调。

```
@Override
public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
    /**
     * 回调
     */
    mOnItemCallbackListener.onMove(viewHolder.getAdapterPosition(), target.getAdapterPosition());
    return true;
}
```

6.让DragVerticalRecyclerViewAdapter实现我们的接口OnItemCallbackListener，并在***onMove()***方法中执行具体移动操作。

```
    @Override
    public void onMove(int fromPosition, int toPosition) {
        /**
         * 在这里进行给原数组数据的移动
         * 第一个参数为数据源
         */
        Collections.swap(itemList, fromPosition, toPosition);
        /**
         * 通知数据移动
         */
        notifyItemMoved(fromPosition, toPosition);
    }
```

7.将DragItemTouchHelperCallback与RecyclerView建立联系。

```
        private ItemTouchHelper mItemTouchHelper;
        //先实例化Callback
        ItemTouchHelper.Callback callback = new DragItemTouchHelperCallback(mAdapter);
        //用Callback构造ItemtouchHelper
        mItemTouchHelper = new ItemTouchHelper(callback);
        //调用ItemTouchHelper的attachToRecyclerView方法建立联系
        mItemTouchHelper.attachToRecyclerView(mRecyclerView);
```

到这里，我们的互换位置就完成了。

当然这里默认是长按才可以拖动。
但是用过QQ的会发现直接点击就可以拖动了。
![](http://upload-images.jianshu.io/upload_images/5361063-6b7ed655e46540bf.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么如何实现呢？让我们继续探索。
很简单，只需下面几步：

1.重写DragItemTouchHelperCallback中的***isLongPressDragEnabled()***方法。

```
    @Override
    public boolean isLongPressDragEnabled() {
        return false;
    }
```

2.在DragVerticalRecyclerViewActivity提供ItemTouchHelper的***get()***方法。

```
    public ItemTouchHelper getItemTouchHelper() {
        return mItemTouchHelper;
    }
```

3.在***onBindViewHolder()***方法中设置触摸监听，然后调用***startDrag()***方法进行移动。

```
        holder.itemView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                ItemTouchHelper itemTouchHelper = ((DragVerticalRecyclerViewActivity) (activity)).getItemTouchHelper();
                if (itemTouchHelper != null) {
                    itemTouchHelper.startDrag(holder);
                }
                return true;
            }
        });
```

如此便实现了。

##### 03.滑动删除

先上图  
![](http://upload-images.jianshu.io/upload_images/5361063-5b6a57c8365879b3.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
这里我们简单实现左滑右滑处理。

我们在原先的基础上做处理。
不过为了避免事件冲突，我们把点击上下拖拽改为长按上下拖拽。

然后说下左右滑动的处理：
1.在DragItemTouchHelperCallback的***getMovementFlags()***方法增加左右滑动。

```
    @Override
    public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
        //设置为可上下拖拽
        int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
        //设置侧滑方向为左右
        int swipeFlags = ItemTouchHelper.START | ItemTouchHelper.END;
        return makeMovementFlags(dragFlags, swipeFlags);
    }
```

2.OnItemCallbackListener接口增加左滑右滑回调。

```
public interface OnItemCallbackListener {
    /**
     * @param fromPosition 起始位置
     * @param toPosition   移动的位置
     */
    void onMove(int fromPosition, int toPosition);

    //右滑
    void onSwipeRight(int position);

    //左滑
    void onSwipeLift(int position);
}
```

3.在DragItemTouchHelperCallback的***onSwiped()***方法中调用。

```
    @Override
    public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
        if (direction == ItemTouchHelper.END) {
            //Item滑动方向为右
            mOnItemCallbackListener.onSwipeRight(viewHolder.getAdapterPosition());
        } else if (direction == ItemTouchHelper.START) {
            //Item滑动方向为左
            mOnItemCallbackListener.onSwipeLift(viewHolder.getAdapterPosition());
        }
    }
```

4.在DragVerticalRecyclerViewAdapter实现左滑右滑接口方法。

```
    @Override
    public void onSwipeRight(int position) {
        itemList.remove(position);
        notifyItemRemoved(position);
        Toast.makeText(activity, "swipe right", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onSwipeLift(int position) {
        itemList.remove(position);
        notifyItemRemoved(position);
        Toast.makeText(activity, "swipe left", Toast.LENGTH_SHORT).show();
    }
```

这里简单做了移除item操作。如果要做一些其他的操作，就在这里的回调进行处理。

其中**notifyItemRemoved(position);**必写。

这样左滑右滑就实现了。是不是很简单。

代码点击👉[源代码](https://github.com/nesger/RecyclerView)


>昵称： 安卓小哥  
邮箱：  nesger.zhan@gmail.com  
博客：  [https://nesger.github.io/](https://nesger.github.io/)  
GitHub: [https://github.com/nesger](https://github.com/nesger)  
