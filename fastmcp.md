<!-- 提示词 -->
# 角色设定
你是一位专业的API转换专家，专注于将FastAPI生成的OpenAPI规范（openapi.json）转换为与OpenAI兼容的格式。你精通OpenAPI 3.0规范、FastAPI框架以及OpenAI的API格式要求。

## 能力
- 准确解析FastAPI生成的openapi.json文件
- 理解OpenAI API格式的特殊要求
- 进行必要的字段映射和格式转换
- 处理认证、端点路径、请求/响应模式等关键元素的转换
- 保持API功能的完整性和一致性

## 知识储备
- 深入掌握OpenAPI 3.0规范
- 熟悉FastAPI框架的特性
- 了解OpenAI API的设计模式和最佳实践
- 掌握JSON Schema和数据模型转换

## 指令
1. 仔细分析输入的FastAPI openapi.json文件
2. 识别所有需要转换的端点、参数和响应结构
3. 按照OpenAI API的格式要求进行转换，包括：
   - 端点路径规范化
   - 参数映射和转换
   - 请求/响应模型适配
   - 错误处理格式调整
   - 认证机制转换
4. 确保转换后的API保持原有功能完整性
5. 输出符合OpenAI API标准的规范文件

## 输出要求
- 提供完整、可直接使用的OpenAI兼容API规范
- 保持JSON格式的规范性和可读性
- 包含所有必要的元数据和文档信息
- 清晰地标注任何需要注意的转换点或限制

## 工作流程
1. 接收用户提供的FastAPI openapi.json文件
2. 分析并确认转换需求
3. 执行转换操作
4. 验证转换结果的正确性
5. 返回最终的OpenAI兼容API规范

请开始处理用户提供的FastAPI openapi.json文件，将其转换为OpenAI兼容格式。

------------------------------------------------------------
from fastapi import FastAPI
from fastapi_mcp import FastApiMCP

app = FastAPI()

mcp = FastApiMCP(
app,
name="My API MCP",
# base_url="http://localhost:8000",
description="My API description",
)

@app.get("/items/")
async def read_items():
    return [{"item_id": "Foo"}]


def use_route_names_as_operation_ids(app: FastAPI) -> None:
    """
    Simplify operation IDs so that generated API clients have simpler function
    names.

    Should be called only after all routes have been added.
    """
    for route in app.routes:
        if isinstance(route, APIRoute):
            route.operation_id = route.name 
            
mcp.mount()

-----------------------------------------------------------------

打开文档http://127.0.0.1:8000/docs#/，点击/openapi.json

拿到
{"openapi":"3.1.0","info":{"title":"FastAPI","version":"0.1.0"},"paths":{"/items/":{"get":{"summary":"Read Items","operationId":"read_items_items__get","responses":{"200":{"description":"Successful Response","content":{"application/json":{"schema":{}}}}}}}}}
喂给大模型转化
{
  "openapi": "3.1.0",
  "info": {
    "title": "FastAPI (Converted for OpenAI)",
    "version": "0.1.0",
    "description": "Converted from FastAPI to OpenAI compatible format"
  },
  "servers": [
    {
      "url": "http://host.docker.internal:8000",
      "description": "Main API server"
    }
  ],
  "paths": {
    "/items": {
      "get": {
        "operationId": "listItems",
        "summary": "Read Items",
        "description": "Retrieve a list of items",
        "responses": {
          "200": {
            "description": "Successful Response",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "data": {
                      "type": "array",
                      "items": {
                        "type": "object"
                      }
                    }
                  }
                }
              }
            }
          },
          "default": {
            "description": "Unexpected error",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Error"
                }
              }
            }
          }
        },
        "x-openai-isConsequential": false
      }
    }
  },
  "components": {
    "schemas": {
      "Error": {
        "type": "object",
        "properties": {
          "error": {
            "type": "object",
            "properties": {
              "message": {
                "type": "string",
                "description": "A human-readable error message"
              },
              "type": {
                "type": "string",
                "description": "The type of error that occurred"
              },
              "param": {
                "type": "string",
                "description": "The parameter that caused the error",
                "nullable": true
              },
              "code": {
                "type": "string",
                "description": "An error code",
                "nullable": true
              }
            },
            "required": [
              "message"
            ]
          }
        },
        "required": [
          "error"
        ]
      }
    }
  },
  "x-openai": {
    "retries": {
      "max_attempts": 3
    }
  }
}
-------------------------------------