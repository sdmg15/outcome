# Detail: Outcome's implementation of Expected
\anchor outcome_expected_reference

You should read <a href="http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0323r1.pdf">P0323R1</a>
if you want a C++ standards level of detail regarding exactly what each member function's
preconditions, effects and postconditions are. Unless otherwise indicated, Outcome's Expected behaves
exactly the same.

For your convenience we copy and paste P0323R1's Expected synopsis here in the left hand column.
In the right hand column we show Outcome's Expected implementation with the lines matched to the
left perfectly (you may need to make your web browser page wider) so you can quickly compare the
LEWG Expected to the Outcome Expected.

As you will note, there are technically speaking quite a lot of deviations. Most are insignificant
and will present no effect in code using Expected. Occasionally a function is missing or implemented
significantly differently. If it has a significantly different signature, it is usually because we
feel the LEWG Expected is defective in that area (e.g. the use of `decay<>` for the factory functions
precludes manufacturing `expected<const T>` which we have found to be in practice very useful). If
it is missing, it is either because we feel its presence is a defect in the LEWG Expected or we think
it not worth implementing.

<hr><br>

\section synopsis_unexpected_type unexpected_type<E>

<table width="100%">
<tr><th width="38%">LEWG's Expected implementation</th><th width="62%">Outcome's Expected implementation</th></tr>
<tr><td width="38%" valign="top">
~~~{.cpp}
namespace std {
namespace experimental {
inline namespace fundamentals_v3 {
  // X.Y.3, Unexpected object type
  template <class E>
    class unexpected_type;

  // X.Y.4, Unexpected factories
  template <class E>
    constexpr unexpected_type<decay_t<E>> make_unexpected(E&& v);
    
    

  // X.Y.5, unexpected_type relational operators
  template <class E>
    constexpr bool
    operator==(const unexpected_type<E>&, const unexpected_type<E>&);
  template <class E>
    constexpr bool
    operator!=(const unexpected_type<E>&, const unexpected_type<E>&);
  template <class E>
    constexpr bool
    operator<(const unexpected_type<E>&, const unexpected_type<E>&);
  template <class E>
    constexpr bool
    operator>(const unexpected_type<E>&, const unexpected_type<E>&);
  template <class E>
    constexpr bool
    operator<=(const unexpected_type<E>&, const unexpected_type<E>&);
  template <class E>
    constexpr bool
    operator>=(const unexpected_type<E>&, const unexpected_type<E>&);

}}}
~~~
</td><td width="62%" valign="top">
~~~{.cpp}
namespace boost {
namespace outcome {
inline namespace v1_xxx {
  // X.Y.3, Unexpected object type
  template <class E>
    using unexpected_type = expected<void, E>;  // NOTE This is a template alias!

  // X.Y.4, Unexpected factories
  template <class E>
    constexpr inline unexpected_type<E> make_unexpected(const E& v);
  template <class E>
    constexpr inline unexpected_type<E> make_unexpected(E&& v);

  // X.Y.5, unexpected_type relational operators
  template <class E>
    constexpr inline bool
    operator==(const unexpected_type<E>&, const unexpected_type<E>&);
  template <class E>
    constexpr inline bool
    operator!=(const unexpected_type<E>&, const unexpected_type<E>&);
  // Other comparison operators not implemented due to
  // https://akrzemi1.wordpress.com/2014/12/02/a-gotcha-with-optional/








    
    
    
}}}
~~~
</td></tr>
<tr><td width="38%" valign="top">
~~~{.cpp}
template <class E>
class unexpected_type {
public:
  unexpected_type() = delete;
  constexpr explicit unexpected_type(const E&);
  constexpr explicit unexpected_type(E&&);
  constexpr const E& value() const;
  constexpr E & value();
private:
  E val; // exposition only
};
~~~
</td><td width="62%" valign="top">
~~~{.cpp}
template <class E>
class unexpected_type {                           // Actually expected<void, E>, below is exposition only
public:
  unexpected_type() = default;
  constexpr explicit unexpected_type(const E&);
  constexpr explicit unexpected_type(E&&);
  constexpr const E& value() const;
  constexpr E & value();
private:
  E val; // exposition only
};
~~~
</td></tr>
</table>


\section synopsis_expected expected<T, E>

<table width="100%">
<tr><th width="38%">LEWG's Expected implementation</th><th width="62%">Outcome's Expected implementation</th></tr>
<tr><td width="38%" valign="top">
~~~{.cpp}
namespace std {
namespace experimental {
inline namespace fundamentals_v3 {
  // X.Z.4, expected for object types
  template <class T, class E= error_condition>
    class expected;

  // X.Z.5, Specialization for void.
  template <class E>
    class expected<void, E>;

  // X.Z.6, unexpect tag
  struct unexpect_t{
    unexpect_t() = delete;
  };
  constexpr unexpect_t unexpect{'implementation defined'};

  // X.Z.7, class bad_expected_access
  class bad_expected_access;

  // X.Z.8, Expected relational operators
  template <class T, class E>
    constexpr bool operator==(const expected<T,E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator!=(const expected<T,E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator<(const expected<T,E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator>(const expected<T,E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator<=(const expected<T,E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator>=(const expected<T,E>&, const expected<T,E>&);

  // X.Z.9, Comparison with T
  template <class T, class E>
    constexpr bool operator==(const expected<T,E>&, const T&);
  template <class T, class E>
    constexpr bool operator==(const T&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator!=(const expected<T,E>&, const T&);
  template <class T, class E>
    constexpr bool operator!=(const T&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator<(const expected<T,E>&, const T&);
  template <class T, class E>
    constexpr bool operator<(const T&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator<=(const expected<T,E>&, const T&);
  template <class T, class E>
    constexpr bool operator<=(const T&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator>(const expected<T,E>&, const T&);
  template <class T, class E>
    constexpr bool operator>(const T&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator>=(const expected<T,E>&, const T&);
  template <class T, class E>
    constexpr bool operator>=(const T&, const expected<T,E>&);

  // X.Z.10, Comparison with unexpected_type<E>
  template <class T, class E>
    constexpr bool operator==(const expected<T,E>&, const unexpected_type<E>&);
  template <class T, class E>
    constexpr bool operator==(const unexpected_type<E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator!=(const expected<T,E>&, const unexpected_type<E>&);
  template <class T, class E>
    constexpr bool operator!=(const unexpected_type<E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator<(const expected<T,E>&, const unexpected_type<E>&);
  template <class T, class E>
    constexpr bool operator<(const unexpected_type<E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator<=(const expected<T,E>&, const unexpected_type<E>&);

  template <class T, class E>
    constexpr bool operator<=(const unexpected_type<E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator>(const expected<T,E>&, const unexpected_type<E>&);
  template <class T, class E>
    constexpr bool operator>(const unexpected_type<E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr bool operator>=(const expected<T,E>&, const unexpected_type<E>&);
  template <class T, class E>
    constexpr bool operator>=(const unexpected_type<E>&, const expected<T,E>&);

  // X.Z.11, Specialized algorithms
  void swap(expected<T,E>&, expected<T,E>&) noexcept(see below);

  // X.Z.12, Factories
  template <class T>
    constexpr expected<decay_t<T>> make_expected(T&& v);
    
    
  expected<void> make_expected();
  template <class T, class E>
    constexpr expected<T, decay_t<E>> make_expected_from_error(E&& e);
    
    
  template <class T, class E, class U>
    constexpr expected<T, E> make_expected_from_error(U&& u);
  template <class F>
    constexpr expected<typename result_type<F>::type>
  make_expected_from_call(F f);

  // X.Z.13, hash support
  template <class T, class E> struct hash<expected<T,E>>;
  template <class E> struct hash<expected<void,E>>;
}}}
~~~
</td><td width="62%" valign="top">
~~~{.cpp}
namespace boost {
namespace outcome {
inline namespace v1_xxx {
  // X.Z.4, expected for object types
  template <class T, class E = error_code_extended>
    using expected = basic_monad<detail::expected_policy<T, E>>;

  // X.Z.5, Specialization for void.
  template <class E>
    class expected<void, E>;

  // X.Z.6, unexpect tag
  using unexpect_t = error_t;  // Tells basic_monad to instantiate an errored instance


  constexpr unexpect_t unexpect{'implementation defined'};

  // X.Z.7, class bad_expected_access
  class bad_expected_access;

  // X.Z.8, Expected relational operators
  template <class T, class E>
    constexpr inline bool operator==(const expected<T,E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr inline bool operator!=(const expected<T,E>&, const expected<T,E>&);
  // Other comparison operators not implemented due to
  // https://akrzemi1.wordpress.com/2014/12/02/a-gotcha-with-optional/
    
    
    
    
    
    
    
  // X.Z.9, Comparison with T
  // Comparison to T operators not implemented due to
  // https://akrzemi1.wordpress.com/2014/12/02/a-gotcha-with-optional/
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  // X.Z.10, Comparison with unexpected_type<E>
  template <class T, class E>
    constexpr inline bool operator==(const expected<T,E>&, const unexpected_type<E>&);
  template <class T, class E>
    constexpr inline bool operator==(const unexpected_type<E>&, const expected<T,E>&);
  template <class T, class E>
    constexpr inline bool operator!=(const expected<T,E>&, const unexpected_type<E>&);
  template <class T, class E>
    constexpr inline bool operator!=(const unexpected_type<E>&, const expected<T,E>&);
  // Other comparison operators not implemented due to
  // https://akrzemi1.wordpress.com/2014/12/02/a-gotcha-with-optional/

  
  
  
  

  
  
  
  
  
  
  
  
  
  
  // X.Z.11, Specialized algorithms
  inline void swap(expected<T,E>&, expected<T,E>&) noexcept(see below);

  // X.Z.12, Factories
  template <class T, class E = error_code_extended>
    constexpr inline expected<T, E> make_expected(const T& v);
  template <class T, class E = error_code_extended>
    constexpr inline expected<T, E> make_expected(T&& v);
  inline expected<void> make_expected();
  template <class T, class E>
    constexpr inline expected<T, E> make_expected_from_error(const E& e);
  template <class T, class E>
    constexpr inline expected<T, E> make_expected_from_error(E&& e);
  template <class T, class E, class U>
    constexpr inline expected<T, E> make_expected_from_error(U&& u);
  // make_expected_from_call() not implemented

  
  
  // X.Z.13, hash support
  // Not implemented as operator< not implemented
  
  
  // Outcome extensions
  
  // NOTE requires state to be set to valued beforehand (and can only deserialise a value)
  template<class Policy> inline std::istream &operator>>(std::istream &s, basic_monad<Policy> &v);
  // Intended for printing rather than serialisation to storage
  template<class Policy> inline std::ostream &operator<<(std::ostream &s, const basic_monad<Policy> &v);
}}}
~~~
</td></tr>
<tr><td width="38%" valign="top">
~~~{.cpp}
template <class T, class E>
class expected
{




public:
  typedef T value_type;
  typedef E error_type;
  
  
  template <class U>
    struct rebind {
      using type = expected<U, error_type>;
    };
    
  // X.Z.4.1, constructors
  constexpr expected() noexcept(see below);
  expected(const expected&);
  expected(expected&&) noexcept(see below);
  constexpr expected(const T&);
  constexpr expected(T&&);
  template <class... Args>
    constexpr explicit expected(in_place_t, Args&&...);
  template <class U, class... Args>
    constexpr explicit expected(in_place_t, initializer_list<U>, Args&&...);
  constexpr expected(unexpected_type<E> const&);
  template <class Err>
    constexpr expected(unexpected_type<Err> const&);
  template <class... Args>
    constexpr explicit expected(unexpect_t, Args&&...);
  template <class U, class... Args>
    constexpr explicit expected(unexpect_t, initializer_list<U>, Args&&...);

  // X.Z.4.2, destructor
  ~expected();

  // X.Z.4.3, assignment
  expected& operator=(const expected&);
  expected& operator=(expected&&) noexcept(see below);
  template <class U> expected& operator=(U&&);
    expected& operator=(const unexpected_type<E>&);
  expected& operator=(unexpected_type<E>&&) noexcept(see below);
  template <class... Args>
    void emplace(Args&&...);
  template <class U, class... Args>
    void emplace(initializer_list<U>, Args&&...);

  // X.Z.4.4, swap
  void swap(expected&) noexcept(see below);

  // X.Z.4.5, observers
  constexpr const T* operator ->() const;
  T* operator ->();
  constexpr const T& operator *() const&;
  T& operator *() &;
  constexpr const T&& operator *() const &&;
  constexpr T&& operator *() &&;
  constexpr explicit operator bool() const noexcept;
  constexpr bool has_value() const noexcept;
  constexpr const T& value() const&;
  constexpr T& value() &;
  constexpr const T&& value() const &&;
  constexpr T&& value() &&;
  constexpr const E& error() const&;
  E& error() &;
  constexpr E&& error() &&;
  constexpr const E&& error() const &&;
  constexpr unexpected_type<E> get_unexpected() const;
  template <class U>
    constexpr T value_or(U&&) const&;
  template <class U>
    T value_or(U&&) &&;

private:
  bool has_value; // exposition only
  union
  {
    value_type val; // exposition only
    error_type err; // exposition only
  };
};
~~~
</td><td width="62%" valign="top">
~~~{.cpp}
template <class T, class E>
class expected
{
  static_assert(!std::is_constructible<T, E>::value, "value_type cannot be constructible from error_type");
  static_assert(!std::is_constructible<E, T>::value, "error_type cannot be constructible from value_type");
  static_assert(std::is_void<E>::value || std::is_nothrow_copy_constructible<E>::value, "error_type must be nothrow copy constructible");
  static_assert(std::is_void<E>::value || std::is_nothrow_move_constructible<E>::value, "error_type must be nothrow move constructible");
public:
  using value_type = devoid<T>;  // If T is void, an unusable but helpfully named type
  using error_type = devoid<E>;  // This traps attempts to use with a useful compiler error
  using raw_value_type = T;
  using raw_error_type = E;
  template <class U> using rebind = expected<U, error_type>;


  
  // X.Z.4.1, constructors
  constexpr expected() noexcept(is_nothrow_default_constructible);
  expected(const expected&) noexcept(is_nothrow_copy_constructible);
  expected(expected&&) noexcept(is_nothrow_move_constructible);
  constexpr expected(const T&) noexcept(T);
  constexpr expected(T&&) noexcept(T);
  template <class... Args>
    constexpr explicit expected(in_place_t, Args&&...);
  template <class U, class... Args>
    constexpr explicit expected(in_place_t, initializer_list<U>, Args&&...);
  constexpr expected(unexpected_type<E> const&) noexcept(is_nothrow_copy_constructible);
  template <class Err>
    constexpr expected(unexpected_type<Err> const&) noexcept(is_nothrow_copy_constructible);

    
    
    
    
    
  // X.Z.4.2, destructor
  ~expected() noexcept(is_nothrow_destructible);

  // X.Z.4.3, assignment
  expected& operator=(const expected&) noexcept(is_nothrow_copy_assignable);
  expected& operator=(expected&&) noexcept(is_nothrow_move_assignable);
  template <class U> expected& operator=(U&&);
    expected& operator=(const unexpected_type<U>&);
  expected& operator=(unexpected_type<E>&&) noexcept(see below);
  template <class... Args>
    void emplace(Args&&...);
  template <class U, class... Args>
    void emplace(initializer_list<U>, Args&&...);

  // X.Z.4.4, swap
  void swap(expected&) noexcept(is_nothrow_move_constructible && is_trivially_destructible);

  // X.Z.4.5, observers
  constexpr const T* operator ->() const;
  constexpr T* operator ->();
  constexpr const T& operator *() const&;
  constexpr T& operator *() &;
  constexpr const T&& operator *() const &&;
  constexpr T&& operator *() &&;
  constexpr explicit operator bool() const noexcept;
  constexpr bool has_value() const noexcept;
  constexpr const T& value() const&;
  constexpr T& value() &;
  constexpr const T&& value() const &&;
  constexpr T&& value() &&;
  constexpr const E& error() const&;
  constexpr E& error() &;
  constexpr E&& error() &&;
  constexpr const E&& error() const &&;
  // get_unexpected() not implemented
  constexpr const value_type &value_or(const value_type &) noexcept const&;
  constexpr value_type& value_or(value_type&) noexcept &;
  constexpr const value_type&& value_or(const value_type&&) noexcept const&&;
  constexpr value_type&& value_or(value_type&&) noexcept &&;

private:
  bool has_value;    // exposition only
  union
  {
    value_type val;  // exposition only
    error_type err;  // exposition only
  };

public:              // Outcome extensions

  // The properties of this monad, and relative to other monads
  static constexpr bool is_default_constructible;
  static constexpr bool is_copy_constructible;
  static constexpr bool is_move_constructible;
  static constexpr bool is_copy_assignable;
  static constexpr bool is_move_assignable;
  static constexpr bool is_nothrow_default_constructible;
  static constexpr bool is_nothrow_copy_constructible;
  static constexpr bool is_nothrow_move_constructible;
  static constexpr bool is_nothrow_copy_assignable;
  static constexpr bool is_nothrow_move_assignable;
  static constexpr bool is_nothrow_destructible;
  static constexpr bool is_trivially_destructible;
  template <class OtherMonad> static constexpr bool is_constructible;
  template <class OtherMonad> static constexpr bool is_comparable;

  // Implicit construction from emptiness, error or excepted state of a void expected
  constexpr expected(const rebind<void>&) noexcept(is_nothrow_copy_constructible);  // use as_void() to create a void expected
  constexpr expected(rebind<void>&&) noexcept(is_nothrow_move_constructible);       // use as_void() to create a void expected
  
  // Implicit construction with error_type
  constexpr expected(const error_type&) noexcept(E);  // use the make_XXX() functions instead
  constexpr expected(error_type&&) noexcept(E);       // use the make_XXX() functions instead

  // Explicit up converting move and copy constructors, only
  // available if T and E are constructible from T2 and E2
  // and no information can ever be lost
  template <class T2, class E2>
    constexpr explicit expected(expected<T2, E2> &&) noexcept(is_nothrow_move_constructible);
  template <class T2, class E2>
    constexpr explicit expected(const expected<T2, E2> &) noexcept(is_nothrow_copy_constructible);
    
  // Observers
  constexpr explicit operator boost_lite::tribool::tribool() const noexcept;   // other if monad empty, false if monad errored/excepted
  constexpr bool is_ready() const noexcept;                                    // i.e. not empty
  constexpr bool empty() const noexcept;
  constexpr bool has_error() const noexcept;

  // Setting value_type, disposes any previous state
  constexpr void set_value(const value_type&);
  constexpr void set_value(value_type&&);
  constexpr void set_value();

  // Retrieving and setting error_type
  constexpr error_type error_or(error_type) noexcept const;
  constexpr void set_error(error_type);
  
  // Clear to empty
  void clear() noexcept(is_nothrow_destructible);

};
~~~
</td></tr>
</table>

\section synopsis_bad_expected_access bad_expected_access<E>

<table width="100%">
<tr><th width="38%">LEWG's Expected implementation</th><th width="62%">Outcome's Expected implementation</th></tr>
<tr><td width="38%" valign="top">
~~~{.cpp}
template <class E>
class bad_expected_access : public logic_error {
public:
  explicit bad_expected_access(E);

  constexpr error_type const& error() const;
  error_type& error();
};
~~~
</td><td width="62%" valign="top">
~~~{.cpp}
template <class E>
class bad_expected_access : public logic_error {
public:
  explicit bad_expected_access(E) noexcept;       // noexcept as this type is thrown as a C++ exception

  const error_type &error() const & noexcept;     // No point in these being constexpr due to inheriting logic_error
  error_type &error() & noexcept;                 // Also some compilers complain loudly if these are constexpr
  const error_type &&error() const && noexcept;
  error_type &&error() && noexcept;
};
~~~
</td></tr>
</table>