# [If-Else Is A Poor Man's Polymorphism](https://levelup.gitconnected.com/if-else-is-a-poor-mans-polymorphism-ab0b333b7265)

```java
if (reason === UserUpdateReason.AddressChanged) {

} else if (reason == UserUpdateReason.UsernameChanged) {

}
```

* Naive implementation that assumes that there'll never be more reasons for a user to change
* When new requirements occur, the answer is not to slap extra `else` / `if`s
* Whenever you have to debug or carry out a bugfix, it'll be surrounded by entirely irrelevant code
* Method signature is lying in that it's not just updating a user - it's also picking which algorithm to execute based on the update reason, and it even knows the implementation of the algorithm
  * The method has a myriad of responsibilities

```java
public interface IUpdateReason {
  Task UpdateAsync(User user);
}

public class AddressChanged : IUpdateReason {
  public Task UpdateAsync(User user) {

  }
}

public class UsernameChanged : IUpdateReason {
  public Task UpdateAsync(User user) {

  }
}
```

* Any new requirements results in a specialized class
