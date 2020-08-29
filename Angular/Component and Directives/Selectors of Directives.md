##Selector Syntax

1) Attribute Directive

a) targets only element with the attribute.
```typescript
@Directive({
selector: '[myDirv]'
})
```
b) targets only div elementw with the attribute.
```typescript
@Directive({
selector: 'div [myDirv]'
})
```

c) targets only div elementw with the class winkle.
```typescript
@Directive({
selector: 'div.myclass'
})
```
d) targets element with the class winkle.
```typescript
@Directive({
selector: 'div.myclass'
})
```

e) Multiple targets element with the class winkle.
```typescript
@Directive({
selector: 'div.myclass, textarea.myclass'
})
```

f) Multiple targets element with the attribute winkle.
```typescript
@Directive({
selector: 'div[myclass], textarea[myclass]'
})
```

g) Not multiple targets
```typescript
@Directive({
selector: `div:not(.container):not(.form-row):not(.form)):not([someattr]), textarea:not([totest])`
})
```
h)  Nested Elements, input element as a content child of au-some-component
```typescript
@Directive({
selector: `au-some-component input`
})
```

