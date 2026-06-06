# [How Shamir's Secret Sharing Works](https://ente.com/blog/how-shamirs-secret-sharing-works/)
* Two distinct points determine exactly one straight line
* Hide a secret where a line crosses the vertical axis
* For example, secret = 7
* Draw a random line through `(0, 7)`
* Give each person a point from the line
* Each person can draw infinite lines through their point
* However, knowing two points gives access to the secret
* This is `2-of-n` secret sharing
* If more people are needed, use a curve
* Parabola needs three points to determine it
* A threshold of `k` points necessary uses a polynomial of degree `k - 1`
