## 编译相关

1. 使用 `platon-cpp` 如何编译多个 `cpp` 文件?

    ```shell
    platon-cpp ./test.cpp ./test1.cpp
    ```

    只允许存在一个合约类。

2. 使用 `platon-cpp` 编译合约, 如何指定wasm输出目录及文件名?

    使用 `-o` 参数, 且指定目录时必须同时指定文件名:

    ```shell
    platon-cpp ./test.cpp -o ./out/test.wasm
    ```
3. platon-truffle执行truffle deploy部署合约失败？

  确认truffle-config.js中连接的链的配制信息及用户的钱包地址是否正确,钱包是否解锁。

4. truffle 部署带参数的构造函数合约失败？


   如果合约中的init函数带有参数，部署合约时需要指定params参数。

5. ABI 支持哪些数据类型?

    生成ABI支持的类型和转换规则如下：

    | 类型             | ABI          |
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
    | bytes            | uint8[]      |
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


## 合约相关

1. 如何实现在 `platon` 进程输出合约 debug 日志？

 - 合约代码首行加上 `#undef NDEBUG`, 必须在头文件 include 之前

 ```cpp
 #undef NDEBUG

 #include <platon/platon.hpp>

 //...
 ```

 - `platon` 启动命令指定日志级别4, 打开 `debug` 标志

 ```shell
 ./platon --debug --verbosity 4 ...
 ```

2. 如何编写合约能有效减少 Gas 消耗？

   - 使用引用参数

   ```cpp
   void test(const std::string& str) {
       // ...
   }
   ```

   - 返回右值引用

   ```cpp
   std::vector<std::string>&& test() {
       std::vector<std::string> v;
       // ...
       return std::move(v);
   }
   ```

   - 尽量不要申请大块内存

3. 使用 `StorageType` 有哪些地方要注意的？

    - 应该在 `init()` 初始化

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

    - 建议直接使用 `StorageType` 的特化类型

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

4. `StorageType` 与 `platon::db::Map`, `platon::db::Array` 有什么区别?

   从底层存储层面来说, `StorageType` 的实现是作为一个整体序列化, 然后存储到数据
   库. 而 `platon::db::Map` 和 `platon::db::Array` 则将容器的每一个元素序列化后, 作为
   K/V 存储到数据库. 对于大规模数据, `platon::db::Map` 和 `platon::db::Array` 性能
   更好.

   在实现合约时, 应根据数据规模, 选择合适的存储结构.

5. RLP 序列化/反序列化 支持哪些 C++ 标准库类型？

    支持以下 C++ 标准库类型:

    - std::string
    - std::vector
    - std::map
    - std::list
    - std::array
    - std::set
    - std::pair
    - std::tuple

6. 自定义类型如何支持 RLP 序列化/反序列化?

   - 普通类型使用宏 `PLATON_SERIALIZE`
   ```cpp
   struct Base {
       int a;
       std::string b;

       PLATON_SERIALIZE(Base, (a)(b));
   };
   ```
   - 派生类使用宏 `PLATON_SERIALIZE_DERIVED`, 同时基类也要使用宏 `PLATON_SERIALIZE`
   ```cpp
   struct Derived : public Base {
       int c;
       int d;

       PLATON_SERIALIZE_DERIVED(Derived, Base, (c)(d));
   };
   ```
