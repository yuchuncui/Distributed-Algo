CSE 535 - Asynchronized System
Assignment - 3

NAME:   Yuchun Cui
SBUID:  108751985


FILES:
----------------------------------------------------
This package includes the following files:

	ramutex_clear.da	ramutex_clear.py
	ramutex_efficient.da	ramutex_efficient.py
	ramutex_alter.da        ramutex_alter.py
	ratoken_clear.da	ratoken_clear.py
	ratoken_efficient.da	ratoken_efficient.py
	ratoken_alter.da      	ratoken_alter.py
	driver.da
	stats.txt		stats.jpg
	README  

USAGE:
----------------------------------------------------

All the programs were compiled and run in Ubuntu 12.04, please use a Linux-like system to test the programs. Two main reasons that Windows is not applicable are:
- The processes wouldn't stop in Windows, though I terminated them in the program, while Linux is good. 
- The output table of the measurement is designed in Ubuntu, so it would generate messy things in Windows. 

To execute the programs:

- You can just run the driver.da, which is able to trigger all the algorithms above at once, by the command: 
	python3.3 -m distalgo.runtime driver.da p1, p2, p3, p4
  Here, p1 - 
Number of Process
	p2 - Numebr of Request
	p3 - Number of Running Times
	p4 - Number of Incrementing Step

  Dealing with invalid input, these parameters are defaultly set to 5, 20, 100, 3
- Actually, the dirver imports the compiled .py files, so I have included all the compiled files of the algorithms above. If you want to test from the .da source file,   please use the following command by steps:
  	python3.3 -m distalgo.compiler ramutex_clear.da
	python3.3 -m distalgo.compiler ramutex_efficient.da
			........
	python3.3 -m distalgo.compiler ratoken_alter.da
	python3.3 -m distalgo.runtime driver.da p1, p2, p3, p4

To check the results:

- Each specific step and time taked by a round is printed to the screen, as well as an output file: ./stats.txt.
- The stats.txt in this package is the testing result on my Ubuntu 12.04, but might be messy to see on Windows. So I attached a screenchot stats.jpg of the table in Ubuntu for you to check on Windows.

MEASUREMENT
------------------------------------------------------

RA's timestamp-based algorithm:

- clear version: 
	It just follows the pseudo-codes in lectures, with quantifications all and any.

- efficient version:
	In the clear version, the condition in the await() has to wait for many replies and check whether they satisfy, which take too long time. As learned from lecture, I set a counter for a single process, it increases by 1 when receiving a reply and the reply satisfies the condition in the await(). Then in the await(), I just need to check whether counter equals to the total number of other processes. This drammatically reduce the running time by 34.56%, according to my test.

- alternative version:
	In this version, I modified the way of terminating the process which just finishes its assigned requests. In previous version, when finishes, the process sends others a quiting message, then it cannot quit until all other processes also send it quiting. 
	I think this might take more time, so I try to terminate the finishing process, and remove it from the process-set, which might reduce some time of sending/receiving messages. But this way suffers from two problems:
	- According to Friday's lecture, I realized it changed the basic pattern of the algorithm.
	- Surprisingly, it turned out that this method didn't work well. It even increase the time, compared with the efficient version, but still less than clear one. I'm still trying to figure this out.

-------------------------------------------------------
RA's token-based algorithm:

- clear version:
	This just follows the pseudo-codes in lectures, and I used two lists of tuples to store the timestamp of requests and tokens, respectively, like: [('10001', 1), ('10002',2), ...]

- efficient version:
	In the clear version, fetching the timestamp of a certain process requires O(len(list)), which could be much shorten by using dictionary in Python, costing O(1) time to retrive a timestamp.
	Moreover, I used another way of terminating processes by adding a counter, increasing by 1 when receiving a quiting message from others. Then it can wait till the counter equaling to the number of the process list. But this conceptually seems nothing significantly reduce the waiting time. 
	With both changes above, the efficient version increased by 3.63%, not a lot.

- alternative version:
	This is improvised by Tengchao Ji, and changed the algorithm a little bit. There exits one situation: if a process just finished CS, but there assigned it a new request, it has to wait for another requset-respond-check. What if we just let it go if this process just continue his new coming request? So I set a boolean to lable this situation, and let the process to keep its token held to enter the CS again. After test, I found a 0.002s/round quicker than before, averagly.  

	
