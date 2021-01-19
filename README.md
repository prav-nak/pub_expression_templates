# Expression templates
Expression templates are "structures representing a computation at compile time, which structures are evaluated only as needed to produce efficient code for the entire computation". Expression templates support lazy evaluation and in the centre of this repo. Expression templates help you get rid of superfluous temporary objects in expressions. 

We use CRTP to define our unary and binary expressions as follows:

```cpp
template <class Derived>
struct base_expr;


template <class Derived>
struct base_expr {

	public:
		base_expr() : _v{static_cast<Derived*>(this)}{}
		auto const& self() const {
			return ( static_cast<const Derived&> (*this));
		}

		auto operator()() {
			return self()();
		}

	private:
		Derived* _v;

};

// Operation is a type which in itself is a callable object.
// I evaluate my Operand and whatever it evaluates, I pass it on to the operation
// and I return whatever this operation returns.
template<class Operation, class Operand>
struct unary_expr : public base_expr<unary_expr<Operation, Operand> > {

	explicit unary_expr(const Operand& val) : _val(val) {
	}

	auto operator()() const {
		return _op(_val());
	}

	private:
	Operation _op;
	Operand _val;
};
```
Then we have operations for each template type as follows: 

```cpp
// For every template type, you now have to define the following operations
// For example, if you have a matrix class, then it should contain a + operator
// if you want the following plus_ to be delegated to the underlying types
struct plus_ {
	
	template<typename T, typename U>
	auto operator()(T const& t, U const& u) const {
			return t+u;
	}

};
```

Finally we overload the operators as shown below:
```cpp
template <typename E1, typename E2>
auto operator+(base_expr<E1> const& e1, base_expr<E2> const& e2) {
	return binary_expr<plus_,E1, E2>{e1.self(), e2.self()};
}
```

Such a code would let us capture the entire expression graph and evaluate it only when we want to (that is done by calling the () operator)

*Like other repos, this one is encrypted using git crypt. If you would like to collaborate please contact.*
