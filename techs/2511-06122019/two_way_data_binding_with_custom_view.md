# Tech Link
- https://developer.android.com/topic/libraries/data-binding/two-way
- https://stackoverflow.com/questions/38709819/android-2-way-databinding-with-custom-view-and-custom-attr
# Description
 - Implement two-way data binding for a custom view

## view layout:
 - We have a layout with EditText for two-way data binding

      <androidx.constraintlayout.widget.ConstraintLayout
          xmlns:android="http://schemas.android.com/apk/res/android"
          xmlns:app="http://schemas.android.com/apk/res-auto"
          xmlns:tools="http://schemas.android.com/tools"
          android:layout_width="match_parent"
          android:layout_height="match_parent">

          <TextView
              android:id="@+id/text_title"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:textAllCaps="true"
              android:textColor="@color/silver_chalice"
              android:textSize="@dimen/sp_13"
              app:layout_constraintStart_toStartOf="parent"
              app:layout_constraintTop_toTopOf="parent"
              tools:text="Status" />

          <EditText
              android:id="@+id/edt_filter"
              android:layout_width="0dp"
              android:layout_height="@dimen/dp_34"
              android:layout_marginTop="@dimen/dp_4"
              android:background="@drawable/bg_border_grey"
              android:inputType="text"
              android:maxLines="1"
              app:layout_constraintEnd_toEndOf="parent"
              app:layout_constraintStart_toStartOf="parent"
              app:layout_constraintTop_toBottomOf="@+id/text_title"
              tools:visibility="visible" />

      </androidx.constraintlayout.widget.ConstraintLayout>

## inflate view:

     class FilterView @JvmOverloads constructor(
         context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
     ) : ConstraintLayout(context, attrs, defStyleAttr) {
         init {
             init(attrs)
         }

         private fun init(attrs: AttributeSet?) {
             LayoutInflater.from(context).inflate(R.layout.layout_filter_view, this, true)
             val typedArray = context.obtainStyledAttributes(attrs, R.styleable.FilterView)
             try {
                 edt_filter.setText(typedArray.getString(R.styleable.FilterView_filter_value))
             } finally {
                 typedArray.recycle()
             }
         }

         fun setFilterValue(value: String?) {
             if (value == edt_filter.text.toString()) return // this is very important, if not have your application maybe stopped or lagged
             edt_filter.setText(value)
             edt_filter.setSelection(value?.length ?: 0)
         }

         fun getFilterValue(): String {
             return edt_filter.text.toString()
         }

         companion object {
             const val TYPE_INPUT = 0
             const val TYPE_SELECT = 1
         }
     }

## binding adapter
- You must implement event filter_valueAttrChanged for two-way data binding

@BindingAdapter("filter_value")
      @JvmStatic
      fun setFilterValue(view: FilterView, newValue: String?) {
          view.setFilterValue(newValue)
      }

      @InverseBindingAdapter(attribute = "filter_value")
      @JvmStatic
      fun getFilterValue(view: FilterView): String? {
          return view.getFilterValue()
      }

      @BindingAdapter(value = ["filter_valueAttrChanged"], requireAll = false)
      @JvmStatic
      fun setListener(view: FilterView, textAttrChanged: InverseBindingListener) {
          view.edt_filter.addTextChangedListener(object : TextWatcher {
              override fun afterTextChanged(after: Editable?) {
                  textAttrChanged.onChange()
              }

              override fun beforeTextChanged(p0: CharSequence?, p1: Int, p2: Int, p3: Int) {
              }

              override fun onTextChanged(p0: CharSequence?, p1: Int, p2: Int, p3: Int) {
              }
          })
      }

## use

      <.FilterView
          android:id="@+id/filter_criteria"
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          app:filter_value="@={viewModel.value}"
          app:layout_constraintEnd_toEndOf="parent"
          app:layout_constraintStart_toStartOf="parent" />
...


# Tech hashtags
- **#Two way data binding**, **#Data binding with custom view**
