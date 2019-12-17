# Tech Link

`Look Deep Into ViewPager2` :
https://proandroiddev.com/look-deep-into-viewpager2-13eb8e06e419

`Khám phá ViewPager 2 chính hãng từ Google` :
https://viblo.asia/p/kham-pha-viewpager-2-chinh-hang-tu-google-yMnKMO0Al7P

`Example: ` 
https://github.com/android/views-widgets-samples/tree/master/ViewPager2

# Description
 - `Introduce Viewpager2`

## New features :
 - ViewPager2 is based on the RecyclerView
 - Right-to-left (RTL) layout support, allows vertical paging that means LayoutManager support
 - Ability to programmatically scroll ViewPager2 (fakeDragBy(offsetPx))
 - MarginPageTransformer introduced to provide an ability to create space between pages (outside of page inset).
 - Implicit usage of getCurrentItem() and setCurrentItem() methods
 - Diff Util support since RecyclerView has part of ViewPager2
 - ItemDecorator introduced with behavior consistent with RecyclerView
 ### Implement from RecylerView.ViewHolder :
 - Create an adapter, it has the same usage as the RecyclerView.Adapter

    ex: ViewPagerAdapter
        class CategoryAdapter : RecyclerView.Adapter<CategoryViewHolder>() {
            var list: List<Category> = listOf()
            override fun onCreateViewHolder(parent: ViewGroup, viewType: Int: CategoryViewHolder {
                return CategoryViewHolder(LayoutInflater.from(parent.context).inflate(R.layout.category_item, parent, false))
            }

            override fun onBindViewHolder(holder: CategoryViewHolder, position: Int) {
                holder.bind(list[position])
            }

            fun setItem(list: List<Category>) {
                this.list = list
                notifyDataSetChanged()
            }

            override fun getItemCount(): Int = list.size
        }

    - ViewHolder is below:

        ex:
        class CategoryViewHolder constructor(itemView: View): RecyclerView.ViewHolder(itemView) {

            fun bind(category: Category) {
                itemView.categoryName.text = category.name
            }
        }

    - Set Adapter to viewPager2: 
        ex: 
            viewPager2.adapter = CategoryAdapter()
            adapter.setItem(categories)

    - We can set vertical scroll easily:
        viewPager2.orientation = ViewPager2.ORIENTATION_VERTICAL

 ### Using OnPageChangeCallback():
    - With the ViewPager2, we don’t need to override all of function in addOnPageChangeListener callback (includes: onPageSelected(), onPageScrollStateChanged() and onPageScrolled()) interface, because OnPageChangeCallback is an abstract static class and no boilerplate and only desired method(s) can be used.
        ex: 
        viewPager2.registerOnPageChangeCallback(object: ViewPager2.OnPageChangeCallback() {
            //override method(s) what you need it
        })

    - onPageSelected: This method will be invoked when a new page becomes selected
    - onPageScrollStateChange: Called when the scroll state changes
    - onPageScrolled: This method will be invoked when the current page is scrolled
    - When you register ViewPager2PageChangeCallback, you can get the position whenever a new page is selected.
        ex: 
        viewPager2PageChangeCallback = ViewPager2PageChangeCallback {pos ->
            viewPager2.setCurrentItem(pos, false)
        }
        viewPager2.registerOnPageChangeCallback(viewPager2PageChangeCallback)
    - And Don’t forget to unregister the OnPageChangeCallback.
        ex :
        override fun onDestroy() {
            super.onDestroy()
            viewPager2.unregisterOnPageChangeCallback(viewPager2PageChangeCallback)
        }

 ### Using Using PageTransformer, MarginPageTransformer, and CompositePageTransformer:

    - Making animation is super easy and more efficient with the help of PageTransformer, MarginPageTransformer, and CompositePageTransformer. You can easily create custom animations with PageTransformer.
        ex :
        class ViewPager2PageTransformation : ViewPager2.PageTransformer {
            override fun transformPage(page: View, position: Float) {
                when {
                    position < -1 ->
                        page.alpha = 0.1f
                    position <= 1 -> {
                        page.alpha = Math.max(0.2f, 1 - Math.abs(position))
                    }
                    else -> page.alpha = 0.1f
                }
            }
        }

### Fake Drag:
    - We can create a fake drag to control the pages
        ex :
        findViewById<View>(R.id.touchpad).setOnTouchListener { _, event ->
            handleOnTouchEvent(event)
        }

        private fun handleOnTouchEvent(event: MotionEvent): Boolean {
            when (event.action) {
                MotionEvent.ACTION_DOWN -> {
                    lastValue = getValue(event)
                    viewPager.beginFakeDrag()
                }

                MotionEvent.ACTION_MOVE -> {
                    val value = getValue(event)
                    val delta = value - lastValue
                    viewPager.fakeDragBy(if (viewPager.isHorizontal) mirrorInRtl(delta) else delta)
                    lastValue = value
                }

                MotionEvent.ACTION_CANCEL, MotionEvent.ACTION_UP -> {
                    viewPager.endFakeDrag()
                }
            }
            return true
        }


## Note Viewpager2 current version: implementation "androidx.viewpager2:viewpager2:1.0.0"

# Tech hashtags
**#Viewpager2**

