# Recycleview的实现方式
## a.设置recycleview的动画
```java
mRecyclerView.setItemAnimator(new TPFadeInAnimator());
```
## b.批量删除之后，以前使用notifyDataSetChanged()更新,现改为如下代码：
```java
 //其中mOldList和newList是删除前和删除后的集合数据。
 DiffUtil.DiffResult diffResult = DiffUtil.calculateDiff(new DiffCallBack(mOldList, newList), true);
                  mAdapter.setDatas(newList);
                  diffResult.dispatchUpdatesTo(mAdapter);
                  mOldList.clear();
                  mOldList.addAll(newList);
```
## c.修改导入的BaseItemAnimator.java文件
其中在BaseItemAnimator中加入//TODO的地方需要大家各自适配自己的app，动画和上次进入批量一致,具体可以再文件中查看

## 实现原理
1.Recycleview中含有自带的DefaultItemAnimator，继承自SimpleItemAnimator。实现效果和UI有出入，模仿其实现方式修改具体涉及到的动画。

2.DiffUtil工具类是Android v7包提供的工具类，使用方法上面已经给出，我们要做的事就是去继续一个DiffUtil.Callback的类，实现它的四个abstract方法
```java
public abstract static class Callback {
        public abstract int getOldListSize();

        public abstract int getNewListSize();

        public abstract boolean areItemsTheSame(int oldItemPosition, int newItemPosition);

        public abstract boolean areContentsTheSame(int oldItemPosition, int newItemPosition);
    }
 ```
 DiffUtil就是帮我们去调用了Adapter的这4个方法，很显然就是这几个方法都是定向刷新,并且可以实现动画效果
 ```java
adapter.notifyItemRangeInserted(position, count);
adapter.notifyItemRangeRemoved(position, count);
adapter.notifyItemMoved(fromPosition, toPosition);
adapter.notifyItemRangeChanged(position, count, payload)
```

# Listview的实现方式

* 1. 自定义AnimationListView，见附件，代码中TODO的地方需要大家自己适配，动画和上次进入批量一致,具体可以在文件中查看
* 2. 将布局文件中需要有动画的listview改为使用AnimationListView控件
* 3. 批量删除后更新界面不用notifyDataSetChanged，改用如下方式
```java
mListView.manipulate(new AnimationListView.Manipulator<RecordAdatper>() {
                    @Override
                    public void manipulate(RecordAdatper adapter) {
                        //更新数据即可
                        mAdatper.setDatas(mDatas);
                    }
                });
	 // 然后设置selectionMode为fasle
 ```
 * 4.在adapter中重写getItemId()方法，保证每个id唯一
 ```java
	@Override
	public long getItemId(int position) {
            return mDatas.get(position).hashCode();
        }
  ```
  * 5. 防止复用出现alpha=0 看不见条目，在getView()中加入透明度设置：
  ```java
  if (convertView == null) {
            convertView = View.inflate(mContext, R.layout.item_song, null);
            holder = new ViewHolder(convertView);
            convertView.setTag(holder);
        } else {
			// 设置alpha为1 
            if (convertView.getAlpha() == 0){
                convertView.setAlpha(1);
            }
            holder = (ViewHolder) convertView.getTag();
        }
```
