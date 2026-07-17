# markov_model.py 中的 parser（argparse）说明

## 什么是 `parser`？

在 `markov_model.py` 末尾的 `if __name__=='__main__':` 块中，`parser` 是一个 `argparse.ArgumentParser` 实例，用于定义和解析命令行参数。

```python
if __name__=='__main__':
    parser = argparse.ArgumentParser(
        description='Train and guess with a markov model. ')
    # ... 添加各种参数 ...
    main(parser.parse_args())
```

## 它的作用

`parser` 的作用是 **将用户在终端传入的命令行参数解析为一个对象**，然后传递给 `main()` 函数。`main()` 函数再根据这些参数决定执行训练、猜测还是计算概率。

### 工作流程

```
用户输入命令行参数
        │
        ▼
  parser.parse_args()        ← 解析参数
        │
        ▼
  main(args)                 ← args 是一个 Namespace 对象，包含用户传入的值
        │
        ├── 如果 args.train_file 不为 None → 执行 train(args)
        ├── 如果 args.model_file 不为 None → 执行 make_guesser_builder(args)
        └── 否则 → 报错
```

## 如何定义参数

每个参数通过 `parser.add_argument(...)` 添加，例如：

```python
parser.add_argument('-t', '--train-file',      # 短选项和长选项
                    help='Training file...')     # 帮助说明
parser.add_argument('-k', '--k-order',          # 短选项和长选项
                    type=int, default=2,        # 指定类型和默认值
                    help='Markov model order...')
parser.add_argument('-s', '--smoothing',
                    default='none',
                    choices=['none','additive','backoff'],  # 限定可选值
                    help='Smoothing type...')
```

### 常用参数

| 方法参数 | 含义 | 示例 |
|---------|------|------|
| `-t, --train-file` | 短/长选项名称 | 用户通过 `-t data.txt` 传入 |
| `type` | 参数类型（`int`, `str`, `float` 等） | `type=int` 自动将输入转为整数 |
| `default` | 用户未传入时的默认值 | `default=2` |
| `help` | 帮助信息 | `-h` 时显示 |
| `choices` | 限定可选值的列表 | `choices=['list','tsv']` |
| `dest` | 指定存储属性名（未指定时由长选项名决定） | `dest='config_values'` |

## 如何在命令行中使用

### 基本形式

```bash
python markov_model.py [选项] [值]
```

### 查看所有参数说明

```bash
python markov_model.py -h
```

输出内容就是由 `parser.add_argument(...)` 的 `help` 参数自动生成的。

### 实际使用示例

```bash
# 训练模型
python markov_model.py -t train.txt -o model.json -k 2 -s none

# 用模型猜测
python markov_model.py -m model.json -o guesses.txt

# 计算概率
python markov_model.py -m model.json -p test.txt -o probs.txt
```

## `parser` 与其他参数的关系

`parser` 解析出的参数最终通过 `main(args)` 分发：

- `args.train_file` → 传给 `train(args)`  → 用于训练
- `args.model_file` → 传给 `make_guesser_builder(args)` → 用于猜测或概率计算
- `args.config` → 传给 `read_config(args)` → 读取配置文件
- `args.smoothing` → 传给 `MarkovModelBuilder(...)` → 设置平滑方式
- `args.k_order` → 传给 `MarkovModelBuilder(...)` → 设置模型阶数

## 总结

`parser`（`argparse.ArgumentParser`）是 markov_model.py 的命令行参数解析器。它：

1. **定义**了脚本支持的所有命令行选项（`-t`, `-o`, `-m`, `-p`, `-k`, `-c`, `-s`, `-f`, `--cv`, `-l`, `--log-level`）
2. **解析**用户输入的命令行字符串
3. **将结果**传递给 `main()` 函数，控制脚本的运行行为（训练 / 猜测 / 计算概率）