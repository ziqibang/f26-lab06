# Contract Worksheet

One section per milestone. Fill each one in as you go, in order. Write each
prediction before you run anything. That is the part a TA asks about.

Keep it short and specific. Point at methods, call sites, and error text.

---

## Milestone 1: The notes overload

### Prediction (write this before you run the build, and you can deliberate with your agent)

**Will the consumer, untouched, still compile and pass?** Yes or no.

Yes, the consumer will still compile and pass.

**Why.** What does the compiler do with the consumer's existing call sites once
the new overload exists?

The compiler will check the consumer's call against the two different versions
and see how many arguments it has and match it to the respective function. The
consumer always passes 4 arguments, so every call still goes to the original
4-parameter method, and nothing changes for them.

### What happened

**The result.** What the build printed for each module.

```
lab06-api       Tests run: 5, Failures: 0, Errors: 0, Skipped: 0
lab06-consumer  Tests run: 7, Failures: 0, Errors: 0, Skipped: 0
lab06-booking-parent ... SUCCESS
lab06-api .............. SUCCESS
lab06-consumer ......... SUCCESS
BUILD SUCCESS
```

**If your prediction was wrong,** say what you missed.

My prediction was right.

**Is an additive change always safe in Java?** One case where adding something
to an API still breaks a caller, if you can name one.

An additive change is not always safe, because if the additive change is being
implemented by another class that you cannot edit, adding the new function to
the interface would break the other class at compile time. The additive change
in adding a function is only safe for code that calls it, but not for a class
that implements it.

---

## Milestone 2: The request object

### Prediction (write this before you run the build)

**Will the untouched consumer still compile and pass?** Yes or no, and if no,
which module goes red and whether at compile time or test time.

The consumer will not compile and pass. The consumer module fails at compile
time, in `FrontDesk.java`, because the methods it calls were removed.

**Where.** Name the call sites you expect to be affected, if any.

The affected call sites are `FrontDesk.java:27` and `FrontDesk.java:33`, the
two `createBooking` calls.

**What about the tests in `api/`, after you update them?** And whether their
result is evidence about the consumer.

The tests in api/ should compile and pass if everything is done correctly, but
that is not evidence about the consumer, because the api tests only call the
new method and never run the consumer's code.

### Step 1: after the fold

**What the build printed.** Paste it for each module, including file and
line for anything that failed.

`mvn -B clean test`:

lab06-api:
```
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0
```

lab06-consumer:
```
[ERROR] COMPILATION ERROR :
[ERROR] consumer/src/main/java/edu/cmu/cs214/frontdesk/FrontDesk.java:[27,19] method createBooking in interface edu.cmu.cs214.booking.BookingApi cannot be applied to given types;
[ERROR]   required: edu.cmu.cs214.booking.BookingRequest
[ERROR]   found:    java.lang.String,long,long,<nulltype>
[ERROR]   reason: actual and formal argument lists differ in length
[ERROR] consumer/src/main/java/edu/cmu/cs214/frontdesk/FrontDesk.java:[33,19] method createBooking in interface edu.cmu.cs214.booking.BookingApi cannot be applied to given types;
[ERROR]   required: edu.cmu.cs214.booking.BookingRequest
[ERROR]   found:    java.lang.String,long,long,java.lang.String
[ERROR]   reason: actual and formal argument lists differ in length
```

Reactor summary:
```
[INFO] lab06-booking-parent ............................... SUCCESS
[INFO] lab06-api .......................................... SUCCESS
[INFO] lab06-consumer ..................................... FAILURE
[INFO] BUILD FAILURE
```

**Which module's tests ran, and which did not.** And what that tells you about
who can detect a contract break.

The api tests ran and passed, but the consumer tests didn't run because it
didn't compile. This says that the api tests will not notice the contract
broke, but modules that actually call the api will.

### Step 2: the deprecation path

**What you added.** The signatures that came back, and what they delegate to.

**The warnings.** Paste one deprecation warning line from the build log (from
a `mvn -B clean test` run, since a rerun with nothing to compile prints none).

**What the deprecation path resolves.** Who can now build that could not build
during step 1, and who is on which schedule.

**What the warnings accomplish that a README note would not.** Be concrete
about where the warning shows up and who sees it without looking for it.

---

## Milestone 3: The misuse critique

Not coded. One misuse, one redesign, one cost. Discuss it with your TA.

### The misuse

**What is easy to get wrong.** One specific thing about the API surface.

**The call site.** File and line in `consumer/`, with the call. Show the
code that a reader cannot understand without opening the javadoc, or that a
caller could get wrong with the compiler still happy.

**What goes wrong when it happens.** Silent bad behavior, wrong data, a crash
somewhere far away?

### The redesign

**The proposal.** Types, enums, factories, or whatever you are proposing. Show
the new signature and the new call site.

**Why the mistake is now hard or impossible to make.** Point at the mechanism,
such as the compiler, a validating constructor, or an exhaustive switch.

### One tradeoff

**What it costs.** Something real, such as caller ceremony, migration burden
against the deprecation path you just built, or more types for a newcomer to
learn. "No real downside" does not count.

**When the price is worth paying.** A condition under which it is.
