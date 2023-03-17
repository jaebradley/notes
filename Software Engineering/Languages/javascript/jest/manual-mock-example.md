# Manual Mock Example

* [Manual Mock Docs](https://facebook.github.io/jest/docs/en/manual-mocks.html)
* [`ES6` Class Mocks](https://facebook.github.io/jest/docs/en/es6-class-mocks.html)

## Discussion

### `usernameValidator`

`usernameValidator.js` uses [`@octokit/rest`](https://github.com/octokit/rest.js).

```javascript
// usernameValidator.js

import GitHub from '@octokit/rest';

const isValidUsername = async (username) => {
  try {
    await new GitHub().users.getForUser({ username });
    return true;
  } catch (e) {
    return false;
  }
};

export default isValidUsername;
```

### Mocking `@octokit/rest`

Need to mock `@octokit/rest`, specifically, the constructor, the `users` property and the `getForUser` method.

In order to create the manual mock for `@octokit/rest`, create a `rest.js` file in `__mocks__/@octokit`.

```javascript
// __mocks__/@octokit/rest

const getForUser = jest.fn(({ username } = {}) => {
  if (!username) {
    throw new Error(`Unknown username: ${username}`);
  }
});

const constructor = jest.fn(() => ({
  users: { getForUser },
}));

const GitHub = constructor;

export default GitHub;
export { getForUser };
```

* Note how the `@octokit/rest` has a mocked constructor that returns an object with a `users` property that references the mocked `getForUser` method.
* Note also how the mocked `getForUser` method is exported. It'll be used later in a test.

### `usernameValidator.test.js`

```javascript
// usernameValidator.test.js

import GitHub, { getForUser } from '@octokit/rest';
import isValidUsername from './usernameValidator';

jest.mock('@octokit/rest');

describe('usernameValidator', () => {
  beforeEach(() => {
    GitHub.mockClear();
    getForUser.mockClear();
  });

  it('successfully validates username', async () => {
    expect(await isValidUsername('jaebaebae')).toBe(true);
    expect(getForUser).toHaveBeenCalledTimes(1);
    expect(getForUser).toHaveBeenCalledWith({ username: 'jaebaebae' });
  });

  it('fails to validate username', async () => {
    expect(await isValidUsername()).toBe(false);
    expect(getForUser).toHaveBeenCalledTimes(1);
    expect(getForUser).toHaveBeenCalledWith({ username: undefined });
    expect(getForUser).toThrowError('Unknown username: undefined');
  });
});
```

* Note how the mocked `@octokit/rest` client and the mocked `getForUser` are imported.
* Note how `GitHub` and `getForUser` mocks are cleared via `mockClear` in the `beforeEach` so that method call counts can be reset successfully
