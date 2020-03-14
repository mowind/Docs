## About Compile

1. How to use `platon-cpp` for compile multiple `cpp` files?

    ```shell
    platon-cpp ./test.cpp ./test1.cpp
    ```

    Only allowing exists one contract class.

2. How to specified output directory and filename when use `platon-cpp`
   to compile contract?

   Use `-o` flag, and must be specified output directory and filename at the
   same time.

   ```shell
   platon-cpp ./test.cpp -o ./out/test.wasm
   ```

3. What data types does ABI support?

    Generate ABI supported types and conversion rules as follows:

    | Type             | ABI          |
    |------------------|--------------|
    | bool             | bool         |
    | uint8\_t         | uint8        |
    | uint16\_t        | uint16       |
    | uint32\_t        | uint32       |
    | uint64\_t        | uint64       |
    | int8\_t          | int8         |
    | int16\_t         | int16        |
    | int32\_t         | int32        |
    | int64\_t         | int64        |
    | bytes            | uint8\[\]      |
    | std::string      | string       |
    | std::vector<T>   | T[]          |
    | std::array[T, N] | T[N]         |
    | std::pair<T, U>  | pair<T, U    |
    | std::set<T>      | set<T>       |
    | std::map<T, V>   | map<T, V>    |
    | std::list<T>     | list<T>      |
    | FixedHash<N>     | FixedHash<N> |
    | u128             | uint128      |
    | bigint           | uint128      |

## About Contract

1. How to output contract debug logs in the `platon` process?

    - Add `#undef NDEBUG` at the first line of contract codes, and must before
      header file included.

      ```cpp
      #undef NDEBUG

      #include <platon/platon.hpp>

      //...
      ```

    - `platon` startup command specifies log level 4 and enable debug flag.

        ```cpp
        ./platon --debug --verbosity 4 ...
        ```

2. How to write a contract can effectively reduce Gas consumption?

    - Use reference arguments
      ```cpp
      void test(const std::string& str) {}
      ```

    - Return rvalue reference

    ```cpp
    std::vector<std::string>&& test() {
        std::vector<std::string> v;
        // ...
        return std::move(v);
    }
    ```

    - Try not to apply for large blocks of memory

3. What should I pay attention to when using `StorageType`?

   - Should be initialized in `init()`
     ```cpp
    CONTRAT Hello : public Contract {
    public:
        ACTION void init() {
            s_.self() = "test";
        }
    private:
        StorageType<"test"_n, std::string> s_;
    };
    ```

    - It is recommended to use a specialized type of `StorageType` directly

      + Uint8
      + Int8
      + Uint16
      + Int16
      + Uint
      + Int
      + Uint64
      + Int64
      + String
      + Vector
      + Set
      + Map
      + Array
      + Tuple

4. What is the difference between `StorageType` and `platon::db::Map`,
   `platon::db::Array`?

   From the underlying storage level, the implementation of `StorageType` is
   serialized as a whole, and then stored in the database. `platon::db::Map`
   and `platon::db::Array` serialize each element of the container as K/V is
   stored to the database. For large-scale data, `platon::db::Map` and
   `platon::db::Array` perform better.

    When implementing a contract, a suitable storage structure should be
    selected based on the size of the data.

5. RLP serialization/deserialization What C++ standard library types are
   supported?

   The following C ++ standard library types are supported:

   - std::string
   - std::vector
   - std::map
   - std::list
   - std::array
   - std::set
   - std::pair
   - std::tuple

6. How do custom types support RLP serialization/deserialization?

    - Macro `PLATON_SERIALIZE` for common types
    ```cpp
   struct Base {
       int a;
       std::string b;

       PLATON_SERIALIZE(Base, (a)(b));
   };
   ```

   - The derived class uses the macro `PLATON_SERIALIZE_DERIVED`, and the base
     class also uses the macro `PLATON_SERIALIZE`
     ```cpp
   struct Derived : public Base {
       int c;
       int d;

       PLATON_SERIALIZE_DERIVED(Derived, Base, (c)(d));
   };
   ```
