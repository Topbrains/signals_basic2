# Unleashing the Power of Effect(). Signals Part 2

In this app we have a list of integers and we display the list in ascending or descending order depending on if the number of times you have clicked the + button.

We use effects to figure this AND to display a SnackBar message with the list. We provide a button to start the effect and another to stop it.

The list is also watched with the Watch() function.

In this example, we show several uses of the signal package functions.  Among the functions that we use are:

- plain signal()
- listSignal()
- Watch()
- effect()
- and the peek() function of the signal object.

In a previous app we have used the compute() function, which is very practical for when we need to obtain a composite result based on other signals. The compute function gives us a signal composed another other signals. Yes it will automatically update if any of the subscribed signals that compose it update.

We can observe this compute signal, but that is the extent of the reactivity capacities of compute.

Now we need to talk a little about effect.

Effect looks very similar to compute. The difference is, that effect can also execute code.

Once an effect is setup, it can run code or evaluate values whenever any of the involved signals change.

Running code allows the effect function to achieve useful reactivity: you can use it to automatically update local storage whenever a configuration parameter signal changes, to display alert messages when some condition is reached and so on.

Like with all reactive systems care must be taken not to get involved in a recursive infinite loop.

If you do, the compiler will detect it most of the time and you can easily figure not to call the effect from within the same effect and also to make sure you read only some signals used by the effect.

To avoid recursion signals provides two methods:

- the untracked() function: you can use untracked to prevent any subscriptions from happening.

```dart
final counter = signal(0);
final effectCount = signal(0);
final fn = () => effectCount.value + 1;

effect(() {
  print(counter.value);

  // Whenever this effect is triggered, run `fn` that gives new value
  effectCount.value = untracked(fn);
});
``

- the peek() function of any signal: "In the rare instance that you have an effect that should write to another signal based on the previous value, but you don’t want the effect to be subscribed to that signal, you can read a signals’s previous value via signal.peek()."

```dart
final counter = signal(0);
final effectCount = signal(0);

effect(() {
  print(counter.value);

  // Whenever this effect is triggered, increase `effectCount`.
  // But we don't want this signal to react to `effectCount`
  effectCount.value = effectCount.peek() + 1;
});
```

We prefer the peek() function, but remember to use it only when needed.

We were able to perform most of the functions in this app without calling the build method of the StatefulWidget. You can verify this by observing the printout log in the debug console.

Only the play and stop buttons use setState()
