# Connect()


### mapStateToProps
* When is it called?

* 2 parameters vs 1 parameter - what is the difference?
Example:
```javascript
const mapStateToProps = (state: State) => ({})

// vs

const mapStateToProps = (state: State, _: OwnProps) => ({})
```

* Is it good to create and array in mapStateToProps function? Why?

```javascript
const mapStateToProps = (state: State, { id }: OwnProps) => ({
  ids: [...state.ids, id]
})
```

### mapStateToPropsFactory
* When is it called?
* When is returned function called?
* When is it useful?
* Briefly explain good (or bad) usage of Factory
