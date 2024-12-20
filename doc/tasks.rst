Tasks
=====

The main building block of your application is the task. The tasks should be
created in the main function of the application before starting the os. The
number of created tasks must not exceed the defined value `N_TASKS` in
`os_defines.h`. A task is created this way:

.. code:: c

    Msg_t queue[Q_SIZE];
    struct {
     ...
     ...
     ...
    } taskData;

    taskId = task_create( taskProc, &taskData, prio, queue, Q_SIZE, msgSize );

The return value, `taskId`, above is just an `uint8_t` that id used to identify
the task.

`taskProc` is a function pointer to the task procedure that is executed when the
task is scheduled to run.

`&taskData` is a pointer to a user defined data structure holding task data.

`prio` is the task priority in range 1-254, where 1 is highest priority. The
kernel always lets the task with highest priority execute first. There can not
be two tasks with same priority.

`queue` is a pointer to a message queue for the task. If the specified size is
0, no message queue is allocated for the task, and the queue parameter is
ignored.

`Q_SIZE` is the length of the message queue in number of messages. Set to 0 if
no queue should be used.

`msgSize` is the size in bytes of the message type held in the message queue.
Ignored of no queue is used.

Task Procedures
---------------

When a task is created, a pointer to the task procedure is given as a parameter.
Two tasks can reuse the same task procedure. In that case, one must be careful
not to access wrong task data. All task specific data should be accessed through
the data pointer passed to the `task_create()` call. The task procedure can
fetch the task data pointer by calling `task_get_data()`.

The code section of a task procedure must begin and end with the `task_open()`
and `task_close()` macros respectively. A very simple task is shown below:

.. code:: c

    void task(void) {
        task_open();
        ...
        task_close();
    }

This task will execute only once. When the `task_close()` line is executed, the
task is put in the killed state and will not be executed again. If the task has
to synchronize with some external event, we can put a blocking call in the
middle:

.. code:: c

    void task(void) {
        task_open();
        ...
        event_wait(evt);
        ...
        task_close();
    }

This task will run to the `event_wait()` call and then block. The kernel will
schedule other tasks to execute and eventually the event will be signalled which
will put the task back to ready state again. Execution will be resumed after the
`event_wait()` call. Once the `task_close()` statement is reached, the task will
be killed and will not be executed again.

If we want a task to be executed periodically we must put the code into an
endless loop:

.. code:: c

    void task(void) {
        task_open();
        for (;;) {
            ...
            ...
            task_wait(TASK_DELAY_TICKS);
        }
        task_close();
    }

Another example of a task waiting for characters to be received on the serial port:

.. code:: c

    void task(void) {
        uint8_t data;
        task_open();
        for (;;) {
            event_wait(rxEvt);
            uart_get(&data);
            ...
        }
        task_close();
    }

The `rxEvt` may be signalled from the uart ISR using `event_ISR_signal(rxEvt)`.

Task API
--------

The task API consists of the following functions and macros:

 - task_create();
 - task_kill();
 - task_open();
 - task_close();
 - task_suspend();
 - task_resume();
 - task_state_get();
 - task_wait();
 - task_get_data().

.. raw:: pdf

   PageBreak oneColumn

