# Tech Link
`Introduction to Recycler View Item Touch Helper` :
https://www.youtube.com/watch?v=KBAigoivRLI

`Example: ` 

## Swipe
https://codeburst.io/android-swipe-menu-with-recyclerview-8f28a235ff28

## Drag & Drop:
https://medium.com/@ipaulpro/drag-and-swipe-with-recyclerview-b9456d2b1aaf

# Description
 - `Handle RecyclerView Item events (swipe, drag & drop) by using ItemTouchHelper and its CallBacks`

## ItemTouchHelper:
 - Class to add swipe, drag&drop support to RecyclerView
 - Use the ItemTouchHelper.Callback to contract between ItemTouchHelper our application.
 ### ItemTouchHelper.Callback
 - The Callback lets us control which touch behaviors are enabled per each ViewHolder and callbacks when user performs these actions.
 - Override makeMovementFlags to define the direction in which you can drag or swipe

    ex: 
```kt
     override fun getMovementFlags(
        recyclerView: RecyclerView,
        viewHolder: RecyclerView.ViewHolder
    ): Int {
        return makeMovementFlags(0 /*dragFlags*/, LEFT or RIGHT /*swipeFlags*/)
    }
```
 - isLongPressDragEnabled() to specify if we want to drag whehn an item is long pressed.
 - isItemViewSwipeEnabled() to secpify if we want to enable swiping
 ### Drag:
    - If user drags an item, ItemTouchHelper will call onMove(recyclerView, dragged, target) 
    - Upon receiving this callback, we will move the item from old position by dragged.getAdapterPosition() to new position target.getAdapterPosition() in our adapter and also call notifiItemMoved(int,int).
        ex:
```kt
        // in SwipeDragController class (sub class of ItemTouchHelper.CallBack)
        override onMove(recyclerView: RecyclerView, 
            viewHolder: RecyclerView.ViewHolder, 
            target: RecyclerView.ViewHolder): Boolean {

            adapter.onItemMove(viewHolder.getAdapterPosition(), target.getAdapterPosition());

            return true;
        }

        // in our adapter source 
        override onItemMove(fromPosition: Int, toPosition: Int): Boolean {
            if (fromPosition < toPosition) {
                for (int i = fromPosition; i < toPosition; i++) {
                    Collections.swap(items, i, i + 1);
                }
            } else {
                for (int i = fromPosition; i > toPosition; i--) {
                    Collections.swap(items, i, i - 1);
                }
            notifyItemMoved(fromPosition, toPosition);
            return true;
        }

        // It’s very important to call notifyItemRemoved() and 
        // notifyItemMoved() so the Adapter is aware of the changes and updates
```

    - To control where a View can be droppped, we can override canDropOver(RecyclerView, ViewHolder)

 ### Swipe:
    - When a View is swiped, ItemTouchHelper animates it until goes out of bounds, then call onSwiped(ViewHolder, Int).
    - At this point, we can do something and update view with notifyItem...
        Ex:

```kt
        // in SwipeDragController class (sub class of ItemTouchHelper.CallBack)
        override fun onSwiped(viewHolder: RecyclerView.ViewHolder, 
            direction: Int) {
            adapter.onItemDismiss(viewHolder.getAdapterPosition());
        }

        // in our adapter source, remove and update
        override fun onItemDismiss(int position) {
            items.remove(position);
            notifyItemRemoved(position);
        }
```
 - And at last, attach our controller to RecycleView.
    ex:
```kt
    val swipeDragController = SwipeDragController()
        ItemTouchHelper(swipeDragController).attachToRecyclerView(my_recyclerview)
```

# Tech hashtags
**#Swipeable RecyclerView**, **Drag&Drop RecyclerView Item**, **#ItemTouchHelper**

