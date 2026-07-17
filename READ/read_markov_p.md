# Markov 模型脚本参数说明

本文档总结了 [markov_model.py](markov_model.py) 中命令行参数的作用，基于最近一次关于 `parser.add_argument(...)` 的说明整理。

## 1. `-t, --train-file`

- 作用：指定训练文件。
- 适用场景：训练阶段。
- 含义：程序会读取训练集数据，建立 Markov 模型。

示例：

```bash
python markov_model.py -t my_train_passwords_markov.txt -o markov_model.json -k 2 -s none -f tsv
```

## 2. `-o, --ofile`

- 作用：输出文件。
- 适用场景：训练时输出模型文件，猜测时输出猜测结果文件。

示例：

```bash
-o markov_model.json
```

## 3. `-m, --model-file`

- 作用：指定已经训练好的模型文件。
- 适用场景：加载模型做密码猜测或概率计算。

示例：

```bash
-m markov_model.json
```

## 4. `-p, --password-file`

- 作用：指定待评估密码文件。
- 适用场景：不是直接生成猜测，而是计算这些密码的概率。

示例：

```bash
-p test_passwords.txt
```

## 5. `-k, --k-order`

- 作用：设置 Markov 模型的阶数。
- 含义：选定上下文长度。
- 例如：`-k 2` 表示用前一个字符的上下文预测下一个字符。

示例：

```bash
-k 2
```

## 6. `-c, --config`

- 作用：指定配置文件。
- 适用场景：设置字符表、模型配置、猜测器行为等。
- 不传时，程序会使用默认配置。

示例：

```bash
-c configs/password_config.json
```

## 7. `-s, --smoothing`

- 作用：设置平滑方式。
- 可选值：
  - `none`
  - `additive`
  - `backoff`
- 含义：用于处理稀有 n-gram 的概率估计问题。

示例：

```bash
-s none
```

## 8. `-f, --train-format`

- 作用：指定训练文件格式。
- 可选值：
  - `tsv`
  - `list`
- 含义：告诉程序训练数据是表格制表符格式还是列表格式。

示例：

```bash
-f tsv
```

## 9. `--cv, --config-values`

- 作用：临时覆盖配置参数。
- 格式：`NAME=VALUE;NAME2=VALUE`
- 适用场景：不改配置文件，只临时修改部分设置。

示例：

```bash
--cv guesser_class='markov_human';simulated_frequency_optimization=True
```

## 10. `-l, --log-file`

- 作用：把日志输出到文件。
- 不传时，日志会直接打印到控制台。

示例：

```bash
-l markov.log
```

## 11. `--log-level`

- 作用：设置日志级别。
- 常见值：`info`、`warning`、`error`、`debug`。

示例：

```bash
--log-level info
```

## 常见用法

### 训练

```bash
python markov_model.py -t my_train_passwords_markov.txt -o markov_model.json -k 2 -s none -f tsv
```

### 生成猜测

```bash
python markov_model.py -m markov_model.json -o markov_guesses.txt
```

### 计算概率

```bash
python markov_model.py -m markov_model.json -p test_passwords.txt -o markov_probs.txt
```

## 注意事项

- 第二次执行猜测时，如果使用 `-c configs/password_config.json`，必须确保配置里包含有效的 `guesser_class`。
- 在你当前日志中，错误原因是配置文件没有设置 `guesser_class`，导致程序认为它是 `None`，而不是支持的 Markov 猜测器类型。
- 直接省略 `-c` 时，程序会使用默认配置，并默认启用 `markov_human`。
