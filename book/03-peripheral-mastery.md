
# Chapter 3: Peripheral Mastery

## 3.1 GPIO — The Basic Interface Between Firmware and the Physical World

### Introduction

Among all microcontroller peripherals, **GPIO** is the most basic and the most universal. Almost every embedded system, no matter how simple or how advanced, depends on General-Purpose Input/Output pins to interact with the outside world.

GPIO is often introduced through trivial examples such as blinking an LED or reading a push button. While these examples are useful, they can hide the deeper engineering importance of GPIO. In real systems, GPIO is not just for basic input and output. It is the first layer of electrical interaction between firmware and hardware. It defines how the microcontroller senses logic levels, drives external circuits, selects alternate peripheral functions, and participates in system-level control.

A serious embedded engineer must therefore understand GPIO not only as a beginner peripheral, but as a foundational interface that affects hardware design, firmware architecture, debugging strategy, and MCU selection.

---

### What GPIO Means

GPIO stands for **General-Purpose Input/Output**.

These are pins on the microcontroller that can be configured by firmware to behave as:

* digital inputs
* digital outputs
* sometimes alternate-function pins for peripherals
* sometimes analog-capable pins depending on the device

The word “general-purpose” means that the pin is not permanently fixed to one role. Firmware can often choose how that pin behaves.

This flexibility is one of the most powerful features of a microcontroller, because it allows the same chip to be adapted to many different systems.

---

### GPIO as a Hardware–Firmware Boundary

GPIO is one of the clearest examples of hardware–firmware interaction.

From the hardware side:

* the pin is a physical electrical connection
* it has voltage limits, current limits, and electrical behavior

From the firmware side:

* registers define whether it acts as input, output, pull-up, alternate function, or another mode
* software reads or writes logic state through memory-mapped control registers

This means GPIO is not simply a software feature and not simply a hardware feature. It is a direct meeting point between the two.

That is why GPIO deserves serious study even though it looks simple at first.

---

### Digital Input

When a GPIO pin is configured as an **input**, the microcontroller observes the logic level present on the pin.

Typically, firmware reads the pin and interprets the result as:

* logic low
* logic high

This is used for:

* buttons and switches
* digital sensors
* interrupt-triggering signals
* status lines from other devices
* fault or ready indicators

Input mode is conceptually simple, but in practice it depends heavily on electrical conditions. A floating input pin can behave unpredictably if no defined electrical path forces it to a valid logic level.

This is why input design must consider pull-up or pull-down behavior.

---

### Digital Output

When a GPIO pin is configured as an **output**, the microcontroller drives the pin to a defined logic level.

Typically this means firmware can command the pin to:

* output low
* output high

This is used for:

* LEDs
* chip-select lines
* enable lines
* relays through drivers
* logic control signals
* communication protocols implemented in software

Output mode is one of the simplest forms of hardware control, but it also introduces important electrical design concerns. A microcontroller pin can drive only limited current and must not be treated like a power source.

A GPIO pin controls logic-level behavior, not arbitrary external loads.

---

### GPIO Registers and Control Logic

Although register names differ between MCU families, GPIO usually involves a familiar set of functions:

* mode configuration
* input read register
* output write register
* optional output latch register
* optional set/reset or toggle mechanism
* pull-up or pull-down configuration
* alternate function selection

These functions determine:

* whether the pin is input or output
* how the pin behaves electrically
* how firmware reads it
* how firmware writes it
* whether the pin belongs to GPIO or to another peripheral function

This makes GPIO one of the best early examples of how control registers shape hardware behavior.

---

### Input and Output Are Not Just Software Modes

A common beginner mistake is to think input and output are only logical settings. In reality, they represent different internal electrical configurations.

When configured as an input, the pin is placed into a sensing state where it observes external voltage without actively driving the line.

When configured as an output, the pin is connected to internal drive circuitry that pushes the pin toward high or low logic levels.

This distinction matters because changing pin mode changes real electrical behavior, not just software interpretation.

That is why incorrect GPIO configuration can cause serious system problems.

---

### Pull-Up and Pull-Down Resistors

One of the most important practical GPIO concepts is the use of **pull-up** and **pull-down** resistors.

An input pin that is left electrically unconnected may float, meaning it can drift between logic states unpredictably due to noise, leakage, or surrounding electrical conditions.

To prevent this, the line is often forced into a known default state:

* a **pull-up** resistor biases the input toward logic high
* a **pull-down** resistor biases the input toward logic low

Many MCUs provide internal pull-up or pull-down support, which can often be enabled through GPIO configuration registers.

This is a very important engineering feature because it reduces external component count and improves input reliability.

---

### Active-High and Active-Low Logic

GPIO-controlled systems often use either **active-high** or **active-low** logic.

* In active-high logic, the function is considered asserted when the pin is high.
* In active-low logic, the function is considered asserted when the pin is low.

For example:

* an LED may turn on when the output is high
* or the board may be wired so the LED turns on when the output is low

Similarly, a push button may read low when pressed if it is wired with a pull-up resistor.

An embedded engineer must become comfortable with this, because many real boards use active-low design for practical electrical reasons.

This means GPIO understanding includes not only pin control, but also logic interpretation.

---

### Push-Pull and Open-Drain / Open-Collector Style Output

Many MCU families provide different output driver styles.

#### Push-Pull Output

In push-pull mode, the pin is actively driven both high and low. This is the most common general-purpose output mode.

It is suitable for:

* LEDs
* digital control lines
* many logic signals

#### Open-Drain or Equivalent

In open-drain mode, the pin actively pulls low but does not actively drive high. The line is brought high through an external or internal pull-up resistor.

This is important for:

* shared bus lines such as I2C
* level adaptation in some cases
* wired-logic signaling

This is a major example of how GPIO features influence real protocol support. Even when a dedicated peripheral exists, the electrical mode of the pin still matters.

---

### GPIO Speed, Slew Rate, and Drive Strength

In more advanced MCUs, GPIO is not only about direction and logic level. Pins may also have configurable electrical behavior such as:

* output speed
* slew rate
* drive strength

These affect how quickly the pin transitions and how strongly it drives the external circuit.

This matters in systems involving:

* high-speed digital signals
* EMI concerns
* long PCB traces
* power-sensitive designs
* communication buses with timing constraints

A beginner may see GPIO as simply high or low.
An engineer sees that the quality of the transition and the electrical behavior of the pin may also matter.

---

### Alternate Functions

In many MCUs, GPIO pins are multiplexed with peripheral functions.

This means a single pin may act as:

* a normal GPIO
* UART TX or RX
* SPI clock or data
* I2C line
* timer output
* ADC input
* PWM output
* external interrupt source

This is a critical design issue.

The number of peripherals an MCU has on paper is not enough. The real question is whether the needed functions can actually be assigned to available pins without conflict.

This is one of the biggest reasons GPIO matters in MCU selection.

A microcontroller may have:

* enough UARTs
* enough timers
* enough PWM channels

but still be unsuitable for a product if the required pin mapping becomes impractical.

---

### GPIO and Real Board Design

GPIO design is inseparable from hardware design.

A firmware engineer must think about:

* logic voltage compatibility
* whether external pull-ups are needed
* whether the pin can safely source or sink enough current
* whether a transistor or MOSFET driver is needed
* whether the pin default state after reset is safe
* whether boot pins share GPIO functionality
* whether a signal should be filtered, debounced, or protected

A hardware engineer must think about:

* what the MCU can really drive
* whether the chosen pin supports the intended function
* whether reset-time pin behavior could cause unintended output
* whether alternate-function conflicts exist

GPIO is therefore one of the strongest examples of co-design between hardware and firmware.

---

### GPIO and Reset-Time Safety

One of the most overlooked GPIO issues in real systems is what happens **before firmware configures the pin**.

At reset, many pins default to input or high-impedance mode. Some pins may have boot-related behavior. Some may briefly float. Some boards may connect pins to sensitive circuits such as relays, power enables, chip selects, or external drivers.

This means GPIO design must consider startup safety.

A poor design may cause:

* a relay to click unexpectedly
* a chip-select to assert wrongly
* a motor driver to enable at power-up
* an external device to misinterpret a transient state

So GPIO selection and external circuitry must always account for reset behavior, not just normal runtime behavior.

---

### Software Use of GPIO Beyond Simple Input and Output

GPIO is also important because many useful behaviors can be implemented in software when dedicated hardware support is missing or limited.

Examples include:

* bit-banged I2C
* bit-banged SPI
* software UART in limited cases
* timing-based signaling
* custom sensor protocols
* simple handshake interfaces
* manual chip-select sequencing

This is very important for MCU selection.

A weaker MCU may still be usable in a product if:

* timing requirements are modest
* CPU budget is available
* firmware can emulate a missing peripheral safely enough

However, software replacement is not free. It consumes:

* CPU time
* code complexity
* timing margin
* debugging effort

This means GPIO capability is tied not only to what hardware can do directly, but also to what firmware can realistically build on top of it.

---

### GPIO and Interrupt Use

GPIO inputs are often used with external interrupt logic.

This allows firmware to respond to events such as:

* button presses
* edges from sensors
* wake-up signals
* encoder changes
* fault lines

The quality of external interrupt support varies across MCU families, and this can matter a lot in product design.

Questions that matter include:

* which pins support interrupts
* whether rising, falling, or both edges are supported
* whether wake-up from sleep is possible
* how many interrupt lines are truly independent

So even a “simple” GPIO input may become a system-level design decision.

---

### Debouncing and Real Input Reliability

A push button does not produce a clean digital transition. It physically bounces, producing multiple rapid transitions before settling.

This means GPIO input handling often requires **debouncing**, either in:

* hardware
* software
* or both

This is another useful lesson: reading a pin is simple, but reading a real signal correctly may not be.

The same applies to:

* noisy sensor outputs
* long wires
* inductive environments
* mechanically unstable inputs

A strong engineer understands that GPIO input handling is not just reading a bit. It is reading an electrical event through a digital interface.

---

### GPIO and MCU Selection

GPIO is one of the most underestimated selection criteria when choosing a microcontroller.

Many people first compare:

* CPU speed
* Flash size
* RAM size

But in real products, GPIO-related questions often become decisive:

* how many pins are available in the chosen package
* how many are actually usable after power, reset, debug, and oscillator pins are reserved
* whether alternate functions conflict
* whether interrupt-capable pins are sufficient
* whether open-drain or pull-up support is available
* whether 5 V tolerance exists where needed
* whether the pin current capability is adequate
* whether analog and digital functions share pins in problematic ways

A product may fail to fit a chosen MCU not because of CPU weakness, but because of GPIO and pin-mapping limitations.

---

### GPIO and Package-Level Reality

The same MCU family may appear in multiple package sizes, and GPIO availability can vary significantly between them.

This means MCU selection is not only about the family or part number, but also about the package.

A design that works on one development board may become impossible or inefficient on a smaller package because:

* fewer pins are exposed
* some alternate functions disappear
* routing constraints increase
* debug access becomes harder

So GPIO analysis must always consider package-level reality, not only architectural marketing specifications.

---

### Engineering Interpretation

GPIO is the most fundamental peripheral because it is the basic digital interface between the microcontroller and the outside world. It allows the MCU to sense logic conditions, drive outputs, select alternate peripheral functions, and support both direct hardware control and software-emulated interfaces.

Its importance goes far beyond LED blinking. GPIO affects electrical safety, startup behavior, protocol feasibility, board design, pin multiplexing, interrupt handling, and MCU selection. A strong embedded engineer therefore treats GPIO not as a trivial beginner topic, but as a core system-design resource.

---

### Common Mistakes and Misconceptions

A common misconception is that all GPIO pins are interchangeable. In reality, different pins may support different alternate functions, interrupt capabilities, analog modes, or electrical properties.

Another mistake is to assume that if a signal can be wired to any pin physically, the firmware can always use it the same way. Pin multiplexing and package constraints often make that false.

It is also common to overlook reset-time behavior and assume pins are safe before firmware initialization. In real products, that can cause dangerous or confusing startup problems.

Finally, beginners often assume missing hardware peripherals can always be replaced with software GPIO implementations. Sometimes they can, but timing, CPU load, reliability, and maintainability must be considered carefully.

---

### Summary

GPIO is the most basic and universal peripheral in a microcontroller, serving as the primary boundary between firmware and the physical world. Through GPIO, firmware can read digital inputs, drive outputs, select alternate peripheral roles, and even implement certain interfaces in software when dedicated hardware support is limited. Its engineering importance is much broader than simple input and output: GPIO influences electrical behavior, startup safety, interrupt handling, software flexibility, pin multiplexing, package feasibility, and MCU selection. Understanding GPIO deeply is therefore essential not only for low-level programming, but also for practical system design and for choosing the right microcontroller for a real product.

# Chapter 3: Peripheral Mastery

## 3.2 Timers and Counters — The Foundation of Time, Measurement, and Control

### Introduction

After GPIO, the next peripheral an embedded engineer must understand deeply is the **timer/counter**. If GPIO is the basic interface between firmware and the outside world, then timers are the basic interface between firmware and **time**.

Almost every serious embedded system depends on timers. They are used for:

* delays
* periodic task scheduling
* pulse generation
* PWM output
* event counting
* frequency measurement
* pulse-width measurement
* timeout detection
* communication timing
* motor control
* sampling control

Because of this, timers are far more important than their name might suggest. They are not just delay generators. They are one of the main infrastructure peripherals that shape how an embedded system behaves in time.

A strong embedded engineer must therefore understand timers not only as a hardware block, but as a system design resource and a major MCU-selection criterion.

---

### What a Timer Really Is

A timer is a hardware peripheral that changes its internal count value according to a clock source.

At the simplest level, a timer:

* receives clock ticks
* increments or decrements a count register
* compares the count against limits or target values
* triggers events when certain conditions occur

A counter is closely related, but instead of counting internal clock ticks, it often counts external events such as pulses arriving on a pin.

In practice, many MCU peripherals combine both concepts, which is why the name **timer/counter** is common.

---

### Why Timers Matter So Much

A CPU can perform time-based behavior in software, but doing so is inefficient and unreliable for serious work. A hardware timer gives the microcontroller an independent and precise way to track time or count events without constant CPU attention.

This matters because embedded systems are not only about logic. They are about **logic in time**.

A system may need to:

* blink an LED every 500 ms
* generate a 20 kHz PWM signal
* measure a pulse width in microseconds
* sample a sensor every 10 ms
* timeout a communication transaction
* schedule multiple recurring tasks

All of these require timing behavior, and timers are the main hardware tool for achieving it.

---

### Basic Timer Operation

A timer usually has:

* a clock source
* a prescaler
* a count register
* a limit or compare mechanism
* event flags and optionally interrupts

The basic sequence is:

1. the timer receives ticks from a clock source
2. the prescaler may divide that clock
3. the count register updates on each tick
4. when a programmed condition occurs, the timer sets a flag, resets, toggles output, or triggers an interrupt

This structure is one of the most universal patterns across MCU families.

---

### Clock Source and Prescaler

A timer does not create time by itself. It depends on a clock source.

That source may be:

* the main MCU clock
* a divided peripheral clock
* an external clock input
* a low-power oscillator
* another internal clock domain

The **prescaler** divides the incoming frequency before it reaches the counter. This allows a timer to trade resolution for range.

For example:

* a fast clock gives fine timing resolution
* a prescaler slows the count rate, allowing longer measurable intervals

This tradeoff is central to timer use.

---

### Timer Resolution and Range

One of the most important engineering ideas in timer design is the tradeoff between **resolution** and **range**.

* **Resolution** means how small a time step the timer can distinguish
* **Range** means how long the timer can count before overflowing or reaching its limit

A faster timer clock gives better resolution but a shorter maximum count interval.
A slower timer clock gives longer range but lower timing precision.

This tradeoff appears in almost every timer-based design decision.

---

### Timer Width

Timers commonly exist as:

* 8-bit
* 16-bit
* 32-bit
* sometimes chained or extended timers

Timer width determines how many count values are available before overflow or wraparound occurs.

For example:

* an 8-bit timer can count 256 states
* a 16-bit timer can count 65,536 states

This matters greatly in real designs. A timer may be perfectly adequate for fast PWM generation but completely inadequate for long time measurement unless prescaling or software extension is used.

So timer width is a real MCU-selection factor.

---

### Overflow and Update Events

When a timer reaches its maximum count and wraps around, an **overflow** event occurs. Some timers also use update events when they reset at a programmable top value.

These events are useful because they can:

* set a status flag
* generate an interrupt
* trigger another peripheral
* update PWM behavior
* define a periodic timing base

Overflow behavior is one of the simplest ways to create regular periodic events in an embedded system.

---

### Timer Modes

Modern microcontrollers usually support multiple timer modes. Even if names differ, the underlying ideas are similar.

#### Free-Running Mode

The timer counts continuously and wraps around when it reaches its limit.

Useful for:

* measuring elapsed time
* basic timing base
* event timestamping

#### Periodic or Auto-Reload Mode

The timer resets after reaching a programmed value.

Useful for:

* periodic interrupts
* recurring task scheduling
* stable timing intervals

#### Compare Mode

The timer compares its current count to one or more programmed values and triggers an event when they match.

Useful for:

* output toggling
* precise timing events
* PWM generation
* scheduling actions at exact counts

#### Capture Mode

The timer stores its current count value when an external input event occurs.

Useful for:

* pulse-width measurement
* frequency measurement
* timestamping external events

These modes are essential because they show that a timer is not just a clock-based counter. It is a flexible time-event engine.

---

### Timer Interrupts

A timer becomes especially powerful when it can generate interrupts.

This allows firmware to respond to:

* periodic intervals
* overflow events
* compare matches
* capture events
* timeout conditions

Timer interrupts are fundamental to many embedded systems because they provide a hardware-driven time base for the application.

Examples:

* running a 1 ms system tick
* updating control loops periodically
* debouncing inputs
* scheduling communication checks
* maintaining software timers

This is one reason timers are often described as the heartbeat of embedded firmware.

---

### Polling vs Interrupt Use for Timers

Timers can be used through polling or interrupts, and the choice depends on the design.

Polling may be suitable when:

* timing needs are simple
* waiting is short
* system complexity is low
* the timer is used for bring-up or test

Interrupts are usually better when:

* recurring timing is needed
* the CPU must do other work meanwhile
* system responsiveness matters
* multiple timing activities must coexist

In real systems, timers are one of the peripherals most commonly used with interrupts.

---

### Timers as Event Counters

A timer is not limited to counting internal clock ticks. In many MCUs, it can count external transitions.

This makes it useful for:

* counting pulses from sensors
* measuring wheel encoder activity
* counting events over time
* frequency division or event tracking

This is a major extension of timer usefulness, because it means the same peripheral can support both time-domain measurement and external event counting.

---

### Input Capture

**Input capture** is one of the most powerful timer features.

In capture mode, when a selected edge occurs on an input pin, the current timer count is copied into a capture register. This allows firmware to know exactly when the event happened relative to the running timer.

This is useful for:

* measuring period
* measuring pulse width
* decoding time-based signals
* determining signal frequency
* timestamping asynchronous events

Input capture is much more precise than trying to sample a signal in software, because the hardware records the event timing directly.

This is a major MCU-selection feature in systems involving precision measurement.

---

### Output Compare

**Output compare** allows a timer to trigger an action when the count reaches a programmed value.

Depending on the MCU, this may:

* set a flag
* generate an interrupt
* toggle a pin
* clear a pin
* trigger another peripheral

This is useful for:

* periodic waveform generation
* precise delay without busy-waiting
* output scheduling
* PWM foundations
* synchronized timing control

Output compare shows that timers are not only for measurement. They are also for timed action generation.

---

### PWM as a Timer-Based Feature

PWM, or Pulse Width Modulation, is usually built on top of timer hardware. Because PWM is so important, it will later deserve its own section, but it is important to understand here that timers are usually the underlying engine.

A timer generates PWM by:

* defining a counting period
* comparing count value against a duty threshold
* driving output accordingly

This means the quality and flexibility of PWM often depend directly on the quality of the timer subsystem.

So when choosing an MCU for:

* motor control
* LED dimming
* power conversion
* servo control

timer capability matters enormously.

---

### Timers and Software Architecture

Timers shape firmware architecture more than many beginners realize.

They are often used to create:

* system ticks
* task schedulers
* timeout managers
* debouncing frameworks
* periodic sampling loops
* protocol timing logic

This means timer design is not just peripheral setup. It is often part of the entire structure of the firmware.

An engineer who understands timers deeply can build systems that are:

* more responsive
* more modular
* less blocking
* easier to debug
* easier to scale

---

### Timers and Power Management

Timers also play a major role in low-power systems.

A timer may be used to:

* wake the MCU from sleep
* schedule periodic sampling
* maintain low-speed timekeeping
* timeout inactive operations

Some MCUs have low-power timers that can run from dedicated low-frequency clocks while the main CPU clock is stopped.

This is a very important selection criterion for battery-powered products.

An MCU with strong low-power timer support may be far better for a design than one with only higher-speed general timers.

---

### Timer Accuracy and Real-World Limits

A timer’s usefulness depends on the quality of its clock source.

Even if the timer logic is perfect, real timing accuracy depends on:

* clock stability
* oscillator tolerance
* temperature effects
* prescaler settings
* interrupt latency if software response is involved

This is important because many timing problems are not actually timer-register mistakes. They are system-clock or timing-architecture mistakes.

A strong engineer always understands that timer precision comes from both:

* timer hardware
* clock quality

---

### Timer Capability and MCU Selection

Timer quality is one of the most important but often underestimated MCU selection factors.

When evaluating an MCU, it is not enough to ask:

* how many timers does it have?

The deeper questions are:

* what widths are they?
* how many channels per timer?
* is input capture available?
* is output compare available?
* how flexible is PWM?
* can timers trigger ADC or DMA?
* can timers run in low-power modes?
* what clock sources do they support?
* how many independent time bases can the system maintain?
* do advanced timers exist for motor control or complementary PWM?

Two MCUs may both claim to have “3 timers,” but their practical usefulness may differ dramatically.

This makes timer analysis central to product-oriented MCU selection.

---

### Hardware Timer vs Software Delay

A weak design often uses software delay loops for timing. A stronger design uses timers.

Software delay loops:

* waste CPU time
* depend on compiler and clock assumptions
* make systems blocking
* scale poorly

Hardware timers:

* run independently
* allow accurate timing
* support interrupts and scheduling
* scale much better with system complexity

This is one of the most important mindset transitions in embedded work: moving from delay-based coding to timer-based system design.

---

### Timers and Missing Peripheral Features

Timers also help compensate for limitations elsewhere in the MCU.

For example:

* software communication protocols may use timers for timing precision
* capture/compare can help emulate certain measurement behaviors
* periodic timer interrupts can support software scheduling frameworks
* timer outputs can sometimes replace specialized waveform hardware in simpler systems

This means timer richness can partially compensate for other missing features.

So when evaluating an MCU, a good timer subsystem may make a simpler MCU more usable than its headline specs suggest.

---

### Engineering Interpretation

Timers and counters are foundational embedded peripherals because they give the microcontroller structured control over time and events. They are used not only for delays, but for periodic execution, waveform generation, event counting, measurement, scheduling, timeout handling, power-aware wake-up, and protocol timing.

A serious embedded engineer must therefore view timers as core system infrastructure. Their capability strongly affects firmware architecture, real-time behavior, protocol support, power design, and MCU selection. In many products, timer quality matters more than raw CPU speed.

---

### Common Mistakes and Misconceptions

A common misconception is that timers are only for creating delays. In reality, they are one of the most versatile peripherals in the entire MCU.

Another mistake is to compare MCUs by timer count alone. Width, mode support, capture/compare features, channel count, clock flexibility, and low-power operation often matter more than the raw number.

It is also common to rely too heavily on software delay loops in early designs. This may work in simple examples, but it becomes a serious limitation in scalable systems.

Finally, many people forget that timer accuracy depends on clock quality. A well-configured timer attached to a poor clock source will still produce poor timing performance.

---

### Summary

Timers and counters are the main peripherals through which a microcontroller understands and controls time. By counting clock ticks or external events, they support delays, periodic scheduling, event counting, frequency and pulse measurement, PWM generation, timeout handling, and low-power wake-up. Their importance extends far beyond simple delay generation: timers shape firmware architecture, real-time responsiveness, measurement capability, power behavior, and even the feasibility of software-based protocol implementations. For this reason, timer capability is one of the most important criteria in both embedded system design and MCU selection.


---

## 3.3 External Interrupts

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.4 UART

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.5 SPI

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.6 I2C

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.7 ADC

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.8 PWM

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.9 Watchdog Timer

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.10 Low-Power Modes

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.11 DMA

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary

---

## 3.12 Peripheral Design Patterns

### Introduction

### Main Concept

### Internal Logic or Structure

### Engineering Interpretation

### Common Mistakes or Misconceptions

### Summary
