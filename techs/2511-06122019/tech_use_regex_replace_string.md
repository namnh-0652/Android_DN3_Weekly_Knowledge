# Open replace toolbar
Step 1: Ctrl + R / Ctrl + Shift + R to open replace toolbar

Step 2: tick into checkbox "Regex"

# Some cases use regex to replace all strings in one music note
## 1. Add SerializedName
-> Regex:
Search Text: `(.*)va(.)(.*):(.*)`

Replace Text: `@SerializedName("$3")\n$1va$2$3:$4`

Ex:

-> Before

```kt
data class FormData(
    private val id: String,
    override var type: Int,
    val title: String?,
    val description: String?,
    val formType: String?,
    val owner: UserData?,
    val isSend: Boolean?,
    val isDone: Boolean?,
    val isRequired: Boolean?,
    val sections: List<FormSectionData> = mutableListOf(),
    var properties: List<FormPropertyData> = mutableListOf()
) : BaseData()
```

-> After

```kt
data class FormData(
    @SerializedName(" id")
    private val id: String,
    @SerializedName(" type")
    override var type: Int,
    @SerializedName(" title")
    val title: String?,
    @SerializedName(" description")
    val description: String?,
    @SerializedName(" formType")
    val formType: String?,
    @SerializedName(" owner")
    val owner: UserData?,
    @SerializedName(" isSend")
    val isSend: Boolean?,
    @SerializedName(" isDone")
    val isDone: Boolean?,
    @SerializedName(" isRequired")
    val isRequired: Boolean?,
    @SerializedName(" sections")
    val sections: List<FormSectionData> = mutableListOf(),
    @SerializedName(" properties")
    var properties: List<FormPropertyData> = mutableListOf()
) : BaseData()
```

## 2. Replace all old functions by new function
-> Issue:

You have new function. You want to replace all old codes by this function.

New function:
```kt
fun <T> List<T>.toInput(): Input<List<T>> {
    return Input.fromNullable(this)
}
```

Old code: `Input.fromNullable(object)`

New code: `object.toInput()`

-> Regex:

Search Text: `Input.fromNullable\((.*)\)`

Replace Text: `$1.toInput()`

-> Before

```kt
fun convertToSectionQuestionInput(): SectionQuestionInput {
        return SectionQuestionInput(
            Input.fromNullable(content),
            Input.fromNullable(image),
            when (questionType) {
                FormQuestionType.SHORT_ANSWER.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.SHORT_ANSWER.text),
                    Input.fromNullable(FormQuestionType.SHORT_ANSWER.type)
                )
                FormQuestionType.LONG_ANSWER.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.LONG_ANSWER.text),
                    Input.fromNullable(FormQuestionType.LONG_ANSWER.type)
                )
                FormQuestionType.MULTIPLE_CHOICE.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.MULTIPLE_CHOICE.text),
                    Input.fromNullable(FormQuestionType.MULTIPLE_CHOICE.type)
                )
                FormQuestionType.CHECKBOXES.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.CHECKBOXES.text),
                    Input.fromNullable(FormQuestionType.CHECKBOXES.type)
                )
                FormQuestionType.DROPDOWN.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.DROPDOWN.text),
                    Input.fromNullable(FormQuestionType.DROPDOWN.type)
                )
                FormQuestionType.LINEAR_SCALE.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.LINEAR_SCALE.text),
                    Input.fromNullable(FormQuestionType.LINEAR_SCALE.type)
                )
                FormQuestionType.MULTIPLE_CHOICE_GRID.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.MULTIPLE_CHOICE_GRID.text),
                    Input.fromNullable(FormQuestionType.MULTIPLE_CHOICE_GRID.type)
                )
                FormQuestionType.CHECKBOX_GRID.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.CHECKBOX_GRID.text),
                    Input.fromNullable(FormQuestionType.CHECKBOX_GRID.type)
                )
                FormQuestionType.DATE.type -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.DATE.text),
                    Input.fromNullable(FormQuestionType.DATE.type)
                )
                else -> QuestionTypeInput(
                    Input.fromNullable(FormQuestionType.TIME.text),
                    Input.fromNullable(FormQuestionType.TIME.type)
                )
            }, Input.fromNullable(isRequired), 
            questionChoices.map { it.convertToOptionInput() },
            Input.fromNullable(rightChoicesIndex),
            Input.fromNullable(answerChoicesIndex),
            Input.fromNullable(answerText)
        )
}
```
    
-> After

```kt
fun convertToSectionQuestionInput(): SectionQuestionInput {
        return SectionQuestionInput(
            content.toInput(),
            image.toInput(),
            when (questionType) {
                FormQuestionType.SHORT_ANSWER.type -> QuestionTypeInput(
                    FormQuestionType.SHORT_ANSWER.text.toInput(),
                    FormQuestionType.SHORT_ANSWER.type.toInput()
                )
                FormQuestionType.LONG_ANSWER.type -> QuestionTypeInput(
                    FormQuestionType.LONG_ANSWER.text.toInput(),
                    FormQuestionType.LONG_ANSWER.type.toInput()
                )
                FormQuestionType.MULTIPLE_CHOICE.type -> QuestionTypeInput(
                    FormQuestionType.MULTIPLE_CHOICE.text.toInput(),
                    FormQuestionType.MULTIPLE_CHOICE.type.toInput()
                )
                FormQuestionType.CHECKBOXES.type -> QuestionTypeInput(
                    FormQuestionType.CHECKBOXES.text.toInput(),
                    FormQuestionType.CHECKBOXES.type.toInput()
                )
                FormQuestionType.DROPDOWN.type -> QuestionTypeInput(
                    FormQuestionType.DROPDOWN.text.toInput(),
                    FormQuestionType.DROPDOWN.type.toInput()
                )
                FormQuestionType.LINEAR_SCALE.type -> QuestionTypeInput(
                    FormQuestionType.LINEAR_SCALE.text.toInput(),
                    FormQuestionType.LINEAR_SCALE.type.toInput()
                )
                FormQuestionType.MULTIPLE_CHOICE_GRID.type -> QuestionTypeInput(
                    FormQuestionType.MULTIPLE_CHOICE_GRID.text.toInput(),
                    FormQuestionType.MULTIPLE_CHOICE_GRID.type.toInput()
                )
                FormQuestionType.CHECKBOX_GRID.type -> QuestionTypeInput(
                    FormQuestionType.CHECKBOX_GRID.text.toInput(),
                    FormQuestionType.CHECKBOX_GRID.type.toInput()
                )
                FormQuestionType.DATE.type -> QuestionTypeInput(
                    FormQuestionType.DATE.text.toInput(),
                    FormQuestionType.DATE.type.toInput()
                )
                else -> QuestionTypeInput(
                    FormQuestionType.TIME.text.toInput(),
                    FormQuestionType.TIME.type.toInput()
                )
            }, isRequired.toInput(),
            questionChoices.map { it.convertToOptionInput() },
            rightChoicesIndex.toInput(),
            answerChoicesIndex.toInput(),
            answerText.toInput()
        )
}
```
    
## 3. Replace inject() by viewModel()
-> Issue:

Old code: `override val viewModel: CreateFormViewModel by inject()`

New code: `override val viewModel: CreateFormViewModel by viewModel()`

You have 100 line codes like this gotta replace :D

-> Regex: 

Search Text: `override val viewModel: (.*) by inject\(\)`

Replace Text: `override val viewModel: $1 by viewModel()`
