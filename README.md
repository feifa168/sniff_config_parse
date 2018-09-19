# 简介
    sniff_config_parse使用boost/property_tree/json_parser.hpp解析json的动态库，从抓包项目中分离出来


## 功能简介
### 接口ISniffConfig
    ISniffConfig是纯虚函数，提供解析json的接口parse以及解析后返回的结构体SniffProtocoConfig通过get_sniff_proto获取，解析错误通过函数get_error_message获取
``` c++
struct SniffProtoConfig {
    std::string name;
    std::vector<unsigned short> ports;
};

class ISniffConfig
{
public:
    VIRTUAL_METHOD bool  CALL_METHOD parse() PURE_VIRTUAL;
    VIRTUAL_METHOD const SniffProtoConfig CALL_METHOD get_sniff_proto() PURE_VIRTUAL;
    VIRTUAL_METHOD const char*  CALL_METHOD get_error_message() PURE_VIRTUAL;
};
    ```
### 接口实现
**SniffConfigParse继承ISniffConfig**
```c++
class SniffConfigParse : public ISniffConfig
```
**解析步骤**
1. 构造函数传入解析的文件
``` c++
SniffConfigParse(std::string config_file) : config_file_(config_file) {}
```
2. 解析文件，格式如下
``` c++
{
    "proto": {
        "name": "http",
        "port": [80, 8080]
    }
}
```
3. 提供解析结果
```c++
const SniffProtoConfig CALL_METHOD get_sniff_proto() { return sniff_proto_config; }
```
4. 导出函数
```c++
BOOST_DLL_ALIAS(
    sniff_proto::SniffConfigParse::get_sniff_config, // <-- this function is exported with...
    create_parse_config                              // <-- ...this alias name
)
```
***相当于导出C函数，不是C++函数***
```c++
const void * create_parse_config = reinterpret_cast<const void*>(reinterpret_cast<intptr_t>(&sniff_proto::SniffConfigParse::get_sniff_config ));
extern "C" __declspec(dllexport) create_parse_config;
```