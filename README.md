# Rust Atomics and Locks

## TODOs

- [ ] Understand this (from page 26) :
	> To avoid the issue of missing notifications in the brief moment between unlocking a mutex and waiting for a condition variable, condition variables provide a way to atomically unlock the mutex and start waiting. This means there is simply no possible moment for notifications to get lost.
