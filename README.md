#Godes#

Libriary to Build Discrete Event Simulation Models in Go (http://golang.org/)

Copyright (c) 2013 Alex Goussiatiner agoussia@yahoo.com

###Features###
Godes is the general-purpose simulation library which includes the  simulation engine  and building blocks for modeling a wide variety of systems at varying levels of detail.

###Advantages###
* Godes is easy to learn for the people familiar with the Go and the elementary simulation concept
* Godes model executes fast  as Go compiles to machine code.
* Godes model is multiplatform as Go compiler targets the Linux, Mac OS X, FreeBSD, Microsoft Windows, etc
* Godes model can be embedded in various computer systems and over the network
* Speed of the Godes model compilation is high
* Variety of the IDE with debuggers are available for Go and Godes as well
* The Godes model can use all of the GO's features and libraries
* Code Security - the Godes includes the  source code for the library and Go is an open source project supported by Google
* Godes is free open source software under MIT license

###Installation###
* Download, install and test the Go at your machine. See instructions at http://golang.org/doc/install
* Optionally install one of the free Go IDE (i.e.LiteIDE X)
* Download Godes package
* Test one of the examples provided

###Examples###

####Example 0. Covers: Basic Features####
During the working day the visitors are entering the restaurant at random intervals and immideatly get the table.
The inter arrival interval is the random variable with uniform distribution from 0 to 70 minutes.
The last visitor gets admitted not later than 8 hours after the opening.
The simulation itself is terminated when the last visitors enters the restaurant.
```go
package main

import (
	"fmt"
	"godes"
)

// the arrival and service are two random number generators for the uniform  distribution
var arrival *godes.UniformDistr = godes.NewUniformDistr()

// the Visitor is a Runner
// any type of the Runner should be defined as struct
// with the *godes.Runner as anonimous field
type Visitor struct {
	*godes.Runner
	number int
}

var visitorsCount int = 0

func (vst *Visitor) Run() { // Any runner should have the Run method
	fmt.Printf("Visitor %v arrives at time= %6.3f \n", vst.number, godes.GetSystemTime())

}
func main() {
	var shutdown_time float64 = 8 * 60
	godes.Run()
	for {
		//godes.Stime is the current simulation time
		if godes.GetSystemTime() < shutdown_time {
			//the function acivates the Runner
			godes.AddRunner(&Visitor{&godes.Runner{}, visitorsCount})
			//this advance the system time
			godes.Advance(arrival.Get(0, 70))
			visitorsCount++
		} else {
			break
		}
	}
	// waits for all the runners to finish the Run()
	godes.WaitUntilDone()
}

Results
Visitor 0 arrives at time=  0.000 
Visitor 1 arrives at time= 62.761 
Visitor 2 arrives at time= 89.380 
Visitor 3 arrives at time= 133.868 
Visitor 4 arrives at time= 189.023 
Visitor 5 arrives at time= 229.752 
Visitor 6 arrives at time= 291.620 
Visitor 7 arrives at time= 334.445 
Visitor 8 arrives at time= 358.918 
Visitor 9 arrives at time= 381.318 
Visitor 10 arrives at time= 424.361 
Visitor 11 arrives at time= 446.308 
```
####Example 1.  Covers:  Boolean Controls####
The restaurant has only one table to sit on. During the working day the visitors are entering the restaurant at random intervals
and wait for the table to be available. The inter arrival interval is the random variable with uniform distribution from 0 to 70 minutes.
The time spent in the restaurant is the random variable with uniform distribution from 10 to 60 minutes.
The last visitor gets admitted not later than 8 hours after the opening.
The simulation itself is terminated when the last visitors has left the restaurant.
```go
package main

import (
	"fmt"
	"godes"
)

// the arrival and service are two random number generators for the uniform  distribution
var arrival *godes.UniformDistr = godes.NewUniformDistr()
var service *godes.UniformDistr = godes.NewUniformDistr()

// tableBusy is the boolean control variable than can be accessed and changed by number of Runners
var tableBusy *godes.BooleanControl = godes.NewBooleanControl()

// the Visitor is a Runner
// any type of the Runner should be defined as struct // with the *godes.Runner as anonimous field
type Visitor struct {
	*godes.Runner
	number int
}

var visitorsCount int = 0

func (vst Visitor) Run() { // Any runner should have the Run method
	fmt.Printf("Visitor %v arrives at time= %6.3f \n", vst.number, godes.GetSystemTime())
	tableBusy.Wait(false) // this will wait till the tableBusy control becomes false
	tableBusy.Set(true)   // sets the tableBusy control to true - the table is busy
	fmt.Printf("Visitor %v gets the table at time= %6.3f \n", vst.number, godes.GetSystemTime())
	godes.Advance(service.Get(10, 60)) //the function advance the simulation time by the value in the argument
	tableBusy.Set(false)               // sets the tableBusy control to false - the table is idle
	fmt.Printf("Visitor %v leaves at time= %6.3f \n", vst.number, godes.GetSystemTime())
}
func main() {
	var shutdown_time float64 = 8 * 60
	godes.Run()
	for {
		//godes.GetSystemTime() is the current simulation time
		if godes.GetSystemTime() < shutdown_time {
			//the function acivates the Runner
			godes.AddRunner(Visitor{&godes.Runner{}, visitorsCount})
			godes.Advance(arrival.Get(0, 70))
			visitorsCount++
		} else {
			break
		}
	}
	godes.WaitUntilDone() // waits for all the runners to finish the Run()
}


Results
Visitor 0 arrives at time=  0.000 
Visitor 0 gets the table at time=  0.000 
Visitor 0 leaves at time= 59.984 
Visitor 1 arrives at time= 69.978 
Visitor 1 gets the table at time= 69.978 
Visitor 1 leaves at time= 129.261 
Visitor 2 arrives at time= 138.974 
Visitor 2 gets the table at time= 138.974 
Visitor 3 arrives at time= 158.395 
Visitor 2 leaves at time= 162.846 
Visitor 3 gets the table at time= 162.846 
Visitor 4 arrives at time= 172.806 
Visitor 5 arrives at time= 180.486 
Visitor 3 leaves at time= 183.140 
Visitor 4 gets the table at time= 183.140 
Visitor 4 leaves at time= 198.625 
Visitor 5 gets the table at time= 198.625 
Visitor 6 arrives at time= 228.787 
Visitor 5 leaves at time= 243.126 
Visitor 6 gets the table at time= 243.126 
Visitor 7 arrives at time= 275.738 
Visitor 6 leaves at time= 286.662 
Visitor 7 gets the table at time= 286.662 
Visitor 8 arrives at time= 316.774 
Visitor 7 leaves at time= 325.974 
Visitor 8 gets the table at time= 325.974 
Visitor 9 arrives at time= 366.170 
Visitor 8 leaves at time= 371.257 
Visitor 9 gets the table at time= 371.257 
Visitor 10 arrives at time= 377.635 
Visitor 11 arrives at time= 385.511 
Visitor 9 leaves at time= 389.446 
Visitor 10 gets the table at time= 389.446 
Visitor 10 leaves at time= 405.072 
Visitor 11 gets the table at time= 405.072 
Visitor 12 arrives at time= 430.294 
Visitor 11 leaves at time= 447.059 
Visitor 12 gets the table at time= 447.059 
Visitor 13 arrives at time= 447.493 
Visitor 12 leaves at time= 469.345 
Visitor 13 gets the table at time= 469.345 
Visitor 13 leaves at time= 519.771 
```
####Example 2.  Covers:  Queues####
During the four working hours the visitors are entering the restaurant at random intervals and form the arrival queue. 
The inter arrival interval is the random variable with uniform distribution from 0 to 30 minutes. The restaurant employs two waiters who are servicing one visitor in a time. The service time  is the random variable with uniform distribution from 10 to 60 minutes. 
The simulation itself is terminated when 
* Simulation time passes the four hours 
* Both waiters have finished servicing  
* There is no visitors in the arrival queue. 

The model  calculates the average (arithmetic mean) of  the visitors waiting time
```go
package main

import (
	"fmt"
	"godes"
)

// the arrival and service are two random number generators for the uniform  distribution
var arrival *godes.UniformDistr = godes.NewUniformDistr()
var service *godes.UniformDistr = godes.NewUniformDistr()

// true when waiter should act
var waitersSwt *godes.BooleanControl = godes.NewBooleanControl()

// FIFO Queue for the arrived
var visitorArrivalQueue *godes.FIFOQueue = godes.NewFIFOQueue("arrivalQueue")

// the Visitor is a Passive Object
type Visitor struct {
	id int
}

// the Waiter is a Runner
type Waiter struct {
	*godes.Runner
	id int
}

var visitorsCount int = 0
var shutdown_time float64 = 4 * 60

func (waiter *Waiter) Run() {

	for {
		waitersSwt.Wait(true)
		if visitorArrivalQueue.Len() > 0 {
			visitor := visitorArrivalQueue.Get()
			if visitorArrivalQueue.Len() == 0 {
				waitersSwt.Set(false)
			}
			fmt.Printf("Visitor %v is invited by waiter %v at %6.3f \n", visitor.(Visitor).id, waiter.id, godes.GetSystemTime())
			godes.Advance(service.Get(10, 60)) //advance the simulation time by the visitor service time
			fmt.Printf("Visitor %v leaves at= %6.3f \n", visitor.(Visitor).id, godes.GetSystemTime())

		}
		if godes.GetSystemTime() > shutdown_time && visitorArrivalQueue.Len() == 0 {
			fmt.Printf("Waiter  %v ends the work at %6.3f \n", waiter.id, godes.GetSystemTime())
			break
		}
	}
}

func main() {

	var visitor Visitor
	for i := 0; i < 2; i++ {
		godes.AddRunner(&Waiter{&godes.Runner{}, i})
	}
	godes.Run()
	for {

		visitorArrivalQueue.Place(Visitor{visitorsCount})
		fmt.Printf("Visitor %v arrives at time= %6.3f \n", visitor.id, godes.GetSystemTime())
		waitersSwt.Set(true)
		godes.Advance(arrival.Get(0, 30))
		visitorsCount++
		if godes.GetSystemTime() > shutdown_time {
			break
		}
	}
	waitersSwt.Set(true)
	godes.WaitUntilDone() // waits for all the runners to finish the Run()
	fmt.Printf("Average Waiting Time %6.3f  \n", visitorArrivalQueue.GetAverageTime())
}



Results
Visitor 0 arrives at time=  0.000 
Visitor 0 is invited by waiter 0 at  0.000 
Visitor 0 arrives at time= 20.748 
Visitor 1 is invited by waiter 1 at 20.748 
Visitor 0 arrives at time= 43.495 
Visitor 0 leaves at= 44.579 
Visitor 2 is invited by waiter 0 at 44.579 
Visitor 0 arrives at time= 44.983 
Visitor 2 leaves at= 57.059 
Visitor 3 is invited by waiter 0 at 57.059 
Visitor 0 arrives at time= 60.895 
Visitor 1 leaves at= 68.661 
Visitor 4 is invited by waiter 1 at 68.661 
Visitor 0 arrives at time= 90.616 
Visitor 0 arrives at time= 91.911 
Visitor 3 leaves at= 93.579 
Visitor 5 is invited by waiter 0 at 93.579 
Visitor 5 leaves at= 105.738 
Visitor 6 is invited by waiter 0 at 105.738 
Visitor 0 arrives at time= 109.690 
Visitor 0 arrives at time= 125.536 
Visitor 4 leaves at= 128.195 
Visitor 7 is invited by waiter 1 at 128.195 
Visitor 6 leaves at= 145.370 
Visitor 8 is invited by waiter 0 at 145.370 
Visitor 0 arrives at time= 155.007 
Visitor 0 arrives at time= 163.835 
Visitor 7 leaves at= 164.604 
Visitor 9 is invited by waiter 1 at 164.604 
Visitor 0 arrives at time= 165.978 
Visitor 9 leaves at= 189.317 
Visitor 10 is invited by waiter 1 at 189.317 
Visitor 0 arrives at time= 194.411 
Visitor 10 leaves at= 202.889 
Visitor 11 is invited by waiter 1 at 202.889 
Visitor 8 leaves at= 204.489 
Visitor 12 is invited by waiter 0 at 204.489 
Visitor 0 arrives at time= 217.891 
Visitor 12 leaves at= 253.621 
Visitor 13 is invited by waiter 0 at 253.621 
Visitor 11 leaves at= 260.278 
Waiter  1 ends the work at 260.278 
Visitor 13 leaves at= 312.358 
Waiter  0 ends the work at 312.358 
Average Waiting Time 13.847 
```
####Example 3.  Covers: Multiple Runs####

```go
package main

import (
	"fmt"
	"godes"
)

// the arrival and service are two random number generators for the uniform  distribution
var arrival *godes.UniformDistr = godes.NewUniformDistr()
var service *godes.UniformDistr = godes.NewUniformDistr()

// true when waiter should act
var waitersSwt *godes.BooleanControl = godes.NewBooleanControl()

// FIFO Queue for the arrived
var visitorArrivalQueue *godes.FIFOQueue = godes.NewFIFOQueue("0")

// the Visitor is a Passive Object
type Visitor struct {
	id int
}

// the Waiter is a Runner
type Waiter struct {
	*godes.Runner
	id int
}

var visitorsCount int = 0
var shutdown_time float64 = 4 * 60

func (waiter *Waiter) Run() {
	for {
		waitersSwt.Wait(true)
		if visitorArrivalQueue.Len() > 0 {
			visitorArrivalQueue.Get()
			if visitorArrivalQueue.Len() == 0 {
				waitersSwt.Set(false)
			}
			godes.Advance(service.Get(10, 60)) //advance the simulation time by the visitor service time

		}
		if godes.GetSystemTime() > shutdown_time && visitorArrivalQueue.Len() == 0 {
			break
		}

	}
}

func main() {

	for runs := 0; runs < 5; runs++ {
		for i := 0; i < 2; i++ {
			godes.AddRunner(&Waiter{&godes.Runner{}, i})
		}
		godes.Run()
		for {
			//godes.Stime is the current simulation time

			visitorArrivalQueue.Place(Visitor{visitorsCount})
			waitersSwt.Set(true)
			godes.Advance(arrival.Get(0, 30))
			visitorsCount++
			if godes.GetSystemTime() > shutdown_time {
				break
			}
		}
		waitersSwt.Set(true)
		godes.WaitUntilDone() // waits for all the runners to finish the Run()
		fmt.Printf(" Run # %v  %v  \n", runs, visitorArrivalQueue)
		//clear after each run
		arrival.Clear()
		service.Clear()
		waitersSwt.Clear()
		visitorArrivalQueue.Clear()
		godes.Clear()

	}
}

Results
 Run # 0 Average Waiting Time 17.461  
 Run # 1 Average Waiting Time 22.264  
 Run # 2 Average Waiting Time 14.501  
 Run # 3 Average Waiting Time 20.446  
 Run # 4 Average Waiting Time 11.195  
```
####Example 4.  Machine Shop (Covers: Interrupt and Resume) ####
A workshop has *n* identical machines. A stream of jobs (enough to
keep the machines busy) arrives. Each machine breaks down
periodically. Repairs are carried out by one repairman.
The repairman continues them when he is done
with the machine repair. The workshop works continuously.

```go
package main

import (
	"fmt"
	"godes"
)

const PT_MEAN = 10.0          //	Avg. processing time in minutes
const PT_SIGMA = 2.0          //	Sigma of processing time
const MTTF = 300.0            // 	Mean time to failure in minutes
const REPAIR_TIME = 30.0      //	Time it takes to repair a machine in minutes
const REPAIR_TIME_SIGMA = 1.0 //	Sigma of repair time

const NUM_MACHINES = 10
const SHUT_DOWN_TIME = 4 * 7 * 24 * 60

// random generator for the processing time - normal distribution
var processingGen *godes.NormalDistr = godes.NewNormalDistr()

// random generator for the  time   until the next failure for a machine - exponential distribution
var breaksGen *godes.ExpDistr = godes.NewExpDistr()

// true when repairman is available for carrying a repair
var repairManAvailableSwt *godes.BooleanControl = godes.NewBooleanControl()

type Machine struct {
	*godes.Runner
	partsCount int
	number     int
}

func (machine *Machine) Run() {
	for {
		godes.Advance(processingGen.Get(PT_MEAN, PT_SIGMA))
		machine.partsCount = machine.partsCount + 1
		if godes.GetSystemTime() > SHUT_DOWN_TIME {
			break
		}

	}
}

type MachineRepair struct {
	*godes.Runner
	machine *Machine
}

func (machineRepair *MachineRepair) Run() {
	machine := machineRepair.machine
	for {

		godes.Advance(breaksGen.Get(1 / MTTF))

		if machine.GetState() == godes.RUNNER_STATE_SCHEDULED {
			breakTime := godes.GetSystemTime()
			//interrupt machine
			godes.Interrupt(machine)
			repairManAvailableSwt.Wait(true)
			//repair
			repairManAvailableSwt.Set(false)
			godes.Advance(processingGen.Get(REPAIR_TIME, REPAIR_TIME_SIGMA))
			//release repairman
			repairManAvailableSwt.Set(true)
			//resume machine and change the scheduled time to compensate the idle time
			godes.Resume(machine, godes.GetSystemTime()-breakTime)

		}

		if godes.GetSystemTime() > SHUT_DOWN_TIME {
			break
		}
	}
}

func main() {

	var m *Machine
	x := make(map[int]*Machine)
	for i := 0; i < NUM_MACHINES; i++ {
		m = &Machine{&godes.Runner{}, 0, i}
		godes.AddRunner(m)
		godes.AddRunner(&MachineRepair{&godes.Runner{}, m})
		x[i] = m
	}
	repairManAvailableSwt.Set(true)
	godes.Run()
	godes.WaitUntilDone()
	//print results
	for i := 0; i < NUM_MACHINES; i++ {
		m = x[i]
		fmt.Printf(" Machine # %v %v \n", m.number, m.partsCount)
	}

}


Results
 Machine # 0 3636 
 Machine # 1 3635 
 Machine # 2 3643 
 Machine # 3 3634 
 Machine # 4 3652 
 Machine # 5 3590 
 Machine # 6 3593 
 Machine # 7 3662 
 Machine # 8 3664 
 Machine # 9 3618  
```
####Example 5.  Bank Renege (Covers: Wait with timeout) ####
This example models a bank counter and customers arriving at random times. Each customer has a certain patience. It waits to get to the counter until she’s at the end of her tether. If she gets to the counter, she uses it for a while before releasing it.

```go
package main

import (
	"fmt"
	"godes"
)

const NEW_CUSTOMERS = 5          // Total number of customers
const INTERVAL_CUSTOMERS = 12.00 // Generate new customers roughly every x minites
const SERVICE_TIME = 12.0
const MIN_PATIENCE = 1 // Min. customer patience
const MAX_PATIENCE = 3 // Max. customer patience

// random generator for the arrival interval - expovariate distribution
var arrivalGen *godes.ExpDistr = godes.NewExpDistr()

// random generator for the patience time time - uniform distribution
var patienceGen *godes.UniformDistr = godes.NewUniformDistr()

// random generator for the  service time - expovariate distribution
var serviceGen *godes.ExpDistr = godes.NewExpDistr()

// true when Counter
var counterAvailable *godes.BooleanControl = godes.NewBooleanControl()

type Customer struct {
	*godes.Runner
	name int
}

func (customer *Customer) Run() {

	arrivalTime := godes.GetSystemTime()
	patience := patienceGen.Get(MIN_PATIENCE, MAX_PATIENCE)
	fmt.Printf("  %6.3f  Customer %v : Here I am   My patience=%6.3f  \n", godes.GetSystemTime(), customer.name, patience)

	counterAvailable.WaitAndTimeout(true, patience)
	if !counterAvailable.GetState() {
		fmt.Printf("  %6.3f  Customer %v : RENEGED after  %6.3f \n", godes.GetSystemTime(), customer.name, godes.GetSystemTime()-arrivalTime)
	} else {
		counterAvailable.Set(false)

		fmt.Printf("  %6.3f  Customer %v : Waited %6.3f \n", godes.GetSystemTime(), customer.name, godes.GetSystemTime()-arrivalTime)
		godes.Advance(serviceGen.Get(1 / SERVICE_TIME))
		fmt.Printf("  %6.3f  Customer %v : Finished \n", godes.GetSystemTime(), customer.name)
		counterAvailable.Set(true)

	}

}

func main() {
	counterAvailable.Set(true)
	godes.Run()
	for i := 0; i < NEW_CUSTOMERS; i++ {
		godes.AddRunner(&Customer{&godes.Runner{}, i})
		godes.Advance(arrivalGen.Get(1 / INTERVAL_CUSTOMERS))
	}

	godes.WaitUntilDone()

}


Results
  0.000  	Customer 0 : Here I am   My patience= 1.208  
  0.000   	Customer 0 : Waited  0.000 
  4.135  	Customer 0 : Finished 
 13.514  	Customer 1 : Here I am   My patience= 2.473  
 13.514  	Customer 1 : Waited  0.000 
 18.172  	Customer 2 : Here I am   My patience= 1.980  
 19.749  	Customer 1 : Finished 
 19.749  	Customer 2 : Waited  1.576 
 24.204  	Customer 3 : Here I am   My patience= 2.795  
 26.999  	Customer 3 : RENEGED after   2.795 
 27.836  	Customer 2 : Finished 
 40.964  	Customer 4 : Here I am   My patience= 2.090  
 40.964  	Customer 4 : Waited  0.000 
 42.849 	Customer 4 : Finished 
```