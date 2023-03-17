# [Building an Optimistic User Interface in React](https://blog.bitsrc.io/building-an-optimistic-user-interface-in-react-b943656e75e3)

* Twitter-clone to illustrate principles
* Simple app with `likedTweets` array and array of `tweets` where each tweet has a `likes` counter
* When a tweet is liked, `likedTweets` array needs to be updated and the relevant tweet needs to increment it's `likes` counter value

```javascript
onLikeClick = tweetId => {
  this.setState(state => {
    const isLiked = this.state.likedTweets.includes(tweetId);

    return {
      tweets: state.tweets.map(
        tweet => tweet.id === tweetId
          ? {...tweet, likes: tweet.likes + (isLiked ? -1 : 1)}
          : tweet
      ),
      likedTweets: isLiked ? state.likedTweets.filter(id => id !== tweetId) : [...state.likedTweets, tweetId],
    };
  });

  likeTweetRequest(tweetId, true)
    .then(() => console.log('liked tweet'))
    .catch(() => console.error('liking tweet failed'));
}
```

* If tweet is liked, then clicking should unlike it - this is done by removing it via `filter` method from `likedTweets` if it's already liked
* If it is unliked, append it to `likedTweets` array
* Increment / decrement `likes` property as appropriate
* In the event of a request failing, need to revert state which assumed success at time of user interaction
* Can use closer and helper function to basically call `setState` in `catch` to reset the state of a given tweet like

```javascript
.catch(() => {
  this.setState(setLikedTweet(tweetId, isLiked));
});
```

* Need to handle rapidly clicking heart icon multiple times
* Add instance property on app called `likeRequestPending`
* If request is already executing and heart is clicked, early exit - we're waiting for a response from the request
* If request is done executing, then continue previously discussed tweet / likes calculation
* Don't forget to set `likeRequestPending` variable back to `false` at end of click handler
