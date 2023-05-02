Download Link: https://assignmentchef.com/product/solved-cmsc216-project-4-calendar
<br>
<span class="kksr-muted">Rate this product</span>

In this project you will use dynamic memory allocation techniques in order to implement a calendar applica- tion. T

2 Objectives

<ul>

 <li>To practice dynamic memory allocation, function pointers and linked lists.</li>

</ul>

3 Project Files

The files for this project can be found in the project4 directory of the public class directory. Make sure you copy that folder to the 216 folder in your home directory. The files associated with the distribution are:

<ol>

 <li>calendar.h – This file provides prototypes for the functions your must implement. It also provides structure definitions, typedefs, and symbolic constants. You may not modify this file.</li>

 <li>event.h – Defines an Event structure. You may not modify this file.</li>

 <li>.h and .c files associated with the memory checker tool.</li>

 <li>Makefile – This is the file where you will define the project’s makefile. The name of the makefile must start with capital M.</li>

 <li>public01.c, public02.c, public03.c, public04.c, public05.c – The public tests for this project.</li>

 <li>student tests.c – You will write student tests in this file.</li>

</ol>

4 Specifications 4.1 Makefile

Your Makefile should be set up so that all programs are built using separate compilation (i.e., source files are turned into object files, which are then linked together in a separate step). All object files should be built using the following options:

-ansi -Wall -g -O0 -Wwrite-strings -Wshadow -pedantic-errors -fstack-protector-all -Wextra

IMPORTANT: You must implement the Makefile using explicit rules; using implicit rules is not allowed. The Makefile examples associated with the lecture

<pre>http://www.cs.umd.edu/class/spring2018/cmsc216/lectures/Week7/Make.pdf</pre>

rely on explicit rules. You should define your Makefile from the start; if you decide to write your Makefile at the end, make sure you back up your code before you write the Makefile. Every semester we have students that by mistake delete their implementation due to a Makefile error.

1

You will need to have the following targets present in your Makefile:

1. all: make all executables2. public01, public02, public03, public04, public05: each one building a public test 3. student_tests: one of your executables.4. student_tests.o:5. calendar.o:6. my_memory_checker_216.o:7. clean: delete all object files and executables

While you are welcome to add other targets to your Makefile, the ones listed above must be present. Make sure you add all the necessary targets that avoid any unnecessary compilation (hint: use the touch command to check your makefile).

4.2 Calendar Overview

For this project you will implement a calendar application that allows the scheduling of events at specific days. A calendar is defined by the following structures:

<pre>typedef struct event {   char *name;</pre>

<pre>   int start_time, duration_minutes;   void *info;   struct event *next;</pre>

} Event;

<pre>typedef struct calendar {   char *name;</pre>

<pre>   Event **events;   int days, total_events;   int (*comp_func) (const void *ptr1, const void *ptr2);   void (*free_info_func) (void *ptr);</pre>

} Calendar;

An Event structure will represent a node in a linked list. The Event structure provides an event’s name, start time (military time), duration in minutes, a pointer to data that provides information about the event, and a reference to another Event structure.

The Calendar structure keeps track of events by using an array of linked lists (events field). Each array entry is associated with a linked list that represents events scheduled for a particular day. The size of the array corresponds to the number of days the calendar has.

Calendar events can be sorted based on different criteria. The comp func field represents a comparison func- tion (similar to compareTo in Java) that will allow the insertion of events based on the criteria defined by the function. For example, you can have a calendar where events are sorted by duration, and another where events are sorted by name.

Events can be associated with additional information (info field). This information can be a structure, a string, anything. Once an event is no longer needed, the calendar can remove this information if a free function (that knows how to free anything associated with the information) is provided. The free info func field represents this free function.

2

4.3 Calendar Functions

You must write all your functions in a file called calendar.c. Functions rely on the macros SUCCESS and FAILURE defined in calendar.h.

1. int init_calendar(const char *name, int days,int (*comp_func) (const void *ptr1, const void *ptr2),

<pre>                       void (*free_info_func) (void *ptr), Calendar ** calendar);</pre>

This function initializes a Calendar structure based on the parameters provided. The function allocates memory for the following items:

<ol>

 <li>Calendar structure</li>

 <li>name field will be assigned memory necessary to store a copy of the name parameter. The name represents the calendar’s name.</li>

 <li>events field will be assigned memory necessary to represent an array of pointers to Event structures. The size of this array corresponds to the days parameter.</li>

</ol>

The out parameter calendar will provide access to the new Calendar structure. Notice the total number of events will be set to zero. The function will fail and return FAILURE if calendar and/or name are NULL, if the number of days is less than 1, or if any memory allocation fails. Otherwise the function will return SUCCESS.

2. int print_calendar(Calendar *calendar, FILE *output_stream, int print_all);

This function prints, to the designated output stream, the calendar’s name, days, and total number of events if print all is true; otherwise this information will not be printed. Information about each event (name, start time, and duration) will be printed regardless the value of print all. See public tests output for format information. Notice that the heading ”**** Events ****” will always be printed.

This function will fail and return FAILURE if calendar and/or output stream are NULL; otherwise the function will return SUCCESS.

3. int add_event(Calendar *calendar, const char *name, int start_time, int duration_minutes, void *info, int day);

This function adds the specified event to the list associated with the day parameter. The event must be added in increasing sorted order using the comparison function (comp func) that allows comparison of two events. The function must allocate memory for the new event and for the event’s name. Other fields of the event structure will be initialized based on the parameter values.

This function will fail and return FAILURE if calendar and/or name are NULL, start time is invalid (must be a value between 0 and 2400, inclusive), duration minutes is less than or equal to 0, day is less than 1 or greater than the number of calendar days, if the event already exist, or if any memory allocation fails. Otherwise the function will return SUCCESS.

Notice that events are uniquely identified across the calendar by name.

4. int find_event(Calendar *calendar, const char *name, Event **event);

This function will return a pointer (via the out parameter event) to the event with the specified name (if any). If the event parameter is NULL, no pointer will be returned. Notice the out parameter event should not be modified unless the event is found. The function will fail and return FAILURE if calendar and/or name are NULL. The function will return SUCCESS if the event was found and FAILURE otherwise.

3

<ol start="5">

 <li>int find_event_in_day(Calendar *calendar, const char *name, int day, Event **event);This function will return a pointer (via the out parameter event) to the event with the specified name in the specified day (if such event exist). If the event parameter is NULL, no pointer will be returned. Notice the out parameter event should not be modified unless the event is found. The function will fail and return FAILURE if calendar and/or name are NULL, or if the day parameter is less than 1 or greater than the number of calendar days. The function will return SUCCESS if the event was found and FAILURE otherwise.</li>

 <li>int remove_event(Calendar *calendar, const char *name);This function will remove the specified event from the calendar returning any memory allocated for the event. If the event has an info field other than NULL and a free info func exists, the function will be called on the info field. The number of calendar events must be adjusted accordingly. This function will fail and return FAILURE if calendar and/or name are NULL or if the event cannot be found; otherwise the function will return SUCCESS.</li>

 <li>void *get_event_info(Calendar *calendar, const char *name);This function returns the info pointer associated with the specified event. The function returns NULL ifthe event is not found. You can assume the calendar and name parameters are different than NULL.</li>

 <li>int clear_calendar(Calendar *calendar);This function will remove all the event lists associated with the calendar and set them to empty lists. Notice that the array holding the event lists will not be removed. The total number of events will be set to 0. If an event has an info field other than NULL and a free info func exists, the function will be called on the info field. This function will fail and return FAILURE if calendar is NULL; otherwise the function will return SUCCESS.</li>

 <li>int clear_day(Calendar *calendar, int day);This function will remove all the events for the specified day setting the event list to empty. The total number of events will be adjusted accordingly. If an event has an info field other than NULL and a free info func exists, the function will be called on the info field. This function will fail and return FAILURE if calendar is NULL, or if the day is less than 1 or greater than the calendar days; otherwise the function will return SUCCESS.</li>

 <li>int destroy_calendar(Calendar *calendar);This function will return memory that was dynamically-allocated for the calendar. If an event has an info field other than NULL and a free info func exists, the function will be called on the info field. This function will fail and return FAILURE if calendar is NULL; otherwise the function will return SUCCESS.</li>

</ol>

4.4 Calendar Testing

<ol>

 <li>You must define tests for your calendar implementation as described in the file student tests.c. You will be graded on the quality of these tests. The main function in student tests should run all tests, but exit with the EXIT FAILURE code if any of your tests fails, and EXIT SUCCESS otherwise. The student tests.c file in the distribution performs this task, though you are welcome to rewrite it as you add tests.</li>

 <li>You should write student tests as you develop your code. If you need assistance during office hours, we may ask for these tests.</li>

 <li>We expected at least one test for each function you need to implement.</li>

</ol>

4

4. We expect your tests to cover specific cases. For example, your tests should check what takes place when adding the same event twice. If we do not see such a test case you will lose credit. There are other cases we are expecting you to test.

4.5 Dynamic Memory Allocation

5

<ol>

 <li>As with all programs that use dynamic memory allocation, you must avoid memory leaks in your code, in all circumstances.</li>

 <li>Keep in mind that when you run valgrind on code that uses the memory tool we have provided, the tool may detect leaks for memory that valgrind allocates. To test your code using valgrind, disable our memory tool (by commenting out the function calls associated with the tool) and run valgrind. While running valgrind use the suggested options (e.g., valgrind –leak-check=full).</li>

 <li>If a function requires multiple dynamic-memory allocations and one of them fails (malloc/callocs re- turns NULL), you are not responsible for freeing the memory of those allocations that were successful. For example:<pre>     mem1 = malloc(...)  /* This one is successful */     mem2 = malloc(...)  /* This one failed, just return FAILURE (don’t worry about mem1) */</pre></li>

 <li>The best way to check you are using dynamic-memory allocation correctly is to test often.</li>