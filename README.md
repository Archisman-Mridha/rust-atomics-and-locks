# Rust Atomics and Locks

## NOTEs

- In page 68, the author wrote :
	> In this situation, if any of the load operations on thread 2 loads the value from the corresponding store operation of thread 1, the acquire fence on thread 2 happens-before the release fence on thread 1.

	I think it's a misprint (confirmed it from https://youtu.be/-h1oa6GYvV8?si=xq6xu3RV5U0dWySn). It should rather be :
	> In this situation, if any of the load operations on thread 2 loads the value from the corresponding store operation of thread 1, the release fence on thread 1 happens-before the acquire fence on thread 2.

## TODOs

- [ ] Understand this (from page 26) :
	> To avoid the issue of missing notifications in the brief moment between unlocking a mutex and waiting for a condition variable, condition variables provide a way to atomically unlock the mutex and start waiting. This means there is simply no possible moment for notifications to get lost.

- [ ] Understand the `static` keyword in Rust.

- [ ] Checkout the `fetch_update` method for atomics from official Rust docs.

- [x] I don't understand the example given in page 57. The author somewhere said :

	> The basic happens-before rule is that everything that happens within the same thread happens in order. If a thread is executing f( ); g( );, then f( ) happens-before g( ).

	But, we know that the compiler / CPU can reorder instructions in the same thread for optimization purposes, provided the meaning of the program stays the same.

	```rs
	DATA.store(123, Relaxed);
	READY.store(true, Release);
	```

	The operations are independent of each other. So there is a possibility of them being reordered.

	> [!NOTE]
	> Ok, I understand it now!
	> ALL memory operations (both read and write) prior to the RELEASE store operation, get ordered and are visible to any further ACQUIRE load operation for the corresponding variable.
	> Instead of **all memory write operations**, I misunderstood it to be memory write operations specific to that variable only.

- [x] In page 63, the author has written :

	> Since we’re not only sharing the atomic variable containing the pointer, but also the data it points to, we can no longer use relaxed memory ordering
	>  We need to make sure that allocating and initializing the data does not race with reading it. In other words, we need to use release and acquire ordering on the store and load operations, to make sure the compiler and processor won’t break our code by—for example—reordering the store of the pointer and the initialization of the data itself.

	But I don't think the compiler / CPU will reorder those operations because that'll change the actual meaning of the program :

	```rs
	p = Box::into_raw(Box::new(generate_data()));
	if let Err(e) = PTR.compare_exchange(std::ptr::null_mut(), p, Release, Acquire) {
		// Safety: p comes from Box::into_raw right above,
		// and wasn't shared with any other thread.
		drop(unsafe { Box::from_raw(p) });
		p = e;
	}
	```

	> [!NOTE]
	> So ChatGPT says that, using Ordering::Relaxed doesn't guarantee : in which order other threads will SEE the above operations.

-  [ ] Have a basic undertanding of `consume ordering`. This is not important right now, since it's not implemented in Rust.

- [ ] I didn't understand `Ordering::SeqCst` and `SeqCst fences`.
