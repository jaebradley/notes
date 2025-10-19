# [What's the big deal about Deterministic Simulation Testing?](https://notes.eatonphil.com/2024-08-20-deterministic-simulation-testing.html)

## Randomness and time
* DST assumes a global seed for all randomness and that the person executing the simulation controls this seed
  * Seed can change across simulation runs
* Once a bad state is observed as a result of running some simulation, the simulator is allowed to enter the same seed again to help the simulator debug the program
* DST does not mean you can't depend on time - it means you must be able to control the clock during the simulation
* To control randomness / time, use dependency injection by passing the dependency as an explicit parameter
* Instead of referring to a global clock / seed, retrieve the clock or seed from someone

## A distributed system
* Code example that injects random disk, network, and process faults
* Code example has three nodes that run in the same process, on different ports

```
# sim.pseudocode
import "distsys-node.pseudocode"

seed = if os.env.DST_SEED ? int(os.env.DST_SEED) : time.now()
rnd = rnd.new(seed)

while true:
  sim_fd = {
    send(fd, buf) => {
      # Inject random failure.
      if rnd.rand() > .5:
         throw Error('bad write')

      # Inject random latency.
      if rnd.rand() > .5:
        await time.sleep(rnd.rand())

      n_written = assert_ok(os.fd.write(buf))
      return n_written
    },
    recv(fd, buf) => {
      # Inject random failure.
      if rnd.rand() > .5:
         throw Error('bad read')

      # Inject random latency.
      if rnd.rand() > .5:
        await time.sleep(rnd.rand())

      return os.fd.read(buf)
    }
  }
  sim_io = {
    open: (filename) => {
      # Inject random failure.
      if rnd.rand() > .5:
        throw Error('bad open')

      # Inject random latency.
      if rnd.rand() > .5:
        await time.sleep(rnd.rand())

      return sim_fd
    }
  }

  all_ports = [6000, 6001, 6002]
  nodes = [
    await distsys-node.start(sim_io, all_ports[0], all_ports),
    await distsys-node.start(sim_io, all_ports[1], all_ports),
    await distsys-node.start(sim_io, all_ports[2], all_ports),
  ]
  history = []
  try:
    key = rnd.rand_bytes(10)
    value = rnd.rand_bytes(10)
    nodes[rnd.rand_in_range_inclusive(0, len(nodes)].insert(key, value)
    history.add((key, value))
    assert_valid_history(nodes, history)

    # Crash a process every so often
    if rnd.rand() > 0.75:
      node = nodes[rnd.rand_in_range_inclusive(0, 3)]
      node.restart()
  catch (e):
    print("Found logical error with seed: %d", seed)
    throw e
```

## Other sources of non-determinism
* CPU instructions and system calls may not be deterministic (including `malloc`)
* "It's terrifyingly easy to build a DST system that appears to be doing a ton of testing, but actually never explores very much of the state space of your system....Unfortunately, unlike with fuzzing, mere branch coverage in your code is usually a pretty poor signal for the kinds of systems"
* When you mock out the behavior of disk or network IO, the benefits of DST are tied to your understanding of the spectrum of behavior that may happen in the real world
* What are all the possible error conditions? What are the extreme latency bounds? What does data or information corruption look like?
* Only in deterministic simulation testing can you configure these scenarios to happen at configurable regularity
