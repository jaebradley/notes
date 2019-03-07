# Jest Mock Examples

## Implementing libraries

### Implementation
```javascript
import inquirer from 'inquirer';

// Other methods...

const selectLanguage = async () => (
  inquirer.prompt([
    {
      name: 'language',
      message: 'Choose optional language',
      type: 'autocomplete',
      source: someMethod,
    },
  ])
);

export {
  selectLanguage,
  // other exports
};
```

### Test
```javascript
// Other tests...
describe('selectLanguage', () => {
  let inquirerSpy;

  beforeEach(() => {
    inquirerSpy = jest.spyOn(inquirer, 'prompt').mockReturnValue('foobar');
  });

  afterEach(() => {
    inquirerSpy.mockRestore();
  });

  it('should return prompt', async () => {
    const expected = [
      {
        name: 'language',
        message: 'Choose optional language',
        type: 'autocomplete',
        source: applyFiltering,
      },
    ];

    selectLanguage();

    expect(inquirer.prompt).toHaveBeenCalledTimes(1);
    expect(inquirer.prompt).toHaveBeenCalledWith(expected);
  });
});
```
