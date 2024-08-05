
# Pseudo date handling

* * *

Very often running test case requires having correct date in various attributes and in correct format. Date value can be hardcoded, but what may work at the time of writing may already be incorrect next day. For such situation a pseudo date value is introduced in ITF.

Instead of date literal you can use “today” literal. ITF will replace it with with todays date at the time of execution. To be more flexible you can also use “today + n” or “today - n” (n is integer value), which will be calculated to number of days ahead or behind todays date. For example “today + 1” will get calculated to tomorrow every time test case is executed.

Also, the date format may be different depending on installation and attribute. Using pseudo date value you can also provide desired date format to be generated after “:”. For example pseudo value “today+1:dd-mm-yyy” will be translated to 09-05-2023 by ITF since it’s tomorrow as writing this instruction. You should use the format accepted by java java.text.SimpleDateFormat class. The default date format is "dd.MM.yyyy".

Pseudo date value can be used in following places:

* Resource object attribute in mockedAggregation.
* Identity attribute in identity xmls loaded by ITF.
* Link attributes in identity xmls loaded by ITF.
* Create, start and end dates in role assignments validation.
* Date in role detection in role assignments validation.
* Identity attribute in ValidateResult command.
* Link attribute in ValidateResult command.