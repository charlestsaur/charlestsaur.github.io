# Data-Splitting Script

[< Index >](Index.md)

本脚本有四种拆分选择:

脚本会自动生成 `data` 目录, 但请注意如果原本有 `data`, 它会直接覆盖

(所有内容转换时均经拆分, 空行不计)

1 - 所有内容写入 `train.jsonl`; 随机 10% 行的内容写入 `valid.jsonl` (最多允许 10% 的重复); 不生成 `test.jsonl`

2 - 所有内容写入 `train.jsonl`; 随机 10% 行的内容写入 `valid.jsonl` (不允许重复); 不生成 `test.jsonl`

3 - 所有内容写入 `train.jsonl`; 随机 10% 行的内容写入 `valid.jsonl` (最多允许 10% 的重复); 随机 10% 行的内容写入 `test.jsonl` (最多允许 10% 的重复)

4 - 所有内容写入 `train.jsonl`; 随机 10% 行的内容写入 `valid.jsonl` (不允许重复); 随机 10% 行的内容写入 `test.jsonl` (不允许重复)

个人推荐用 3

(虽说测试集应是模型在训练过程中从未见过的数据, 但我这个又不是逻辑有关的微调, 不太需要关联性, 所以偷了个懒, 用的是训练集的数据)

脚本为:

(请将你的 txt 文件命名为 `001.txt`)

```python
import json
import os
import random
from collections import Counter

def create_jsonl_files(input_file, output_dir, mode):

    os.makedirs(output_dir, exist_ok=True)

    with open(input_file, "r", encoding="utf-8") as f:
        lines = [l.strip() for l in f if l.strip()]

    if not lines:
        print("❌ The input file is empty or contains only blank lines")
        return

    train_path = os.path.join(output_dir, "train.jsonl")
    with open(train_path, "w", encoding="utf-8") as train_out:
        for line in lines:
            json.dump({"text": line}, train_out, ensure_ascii=False)
            train_out.write("\n")

    valid_size = max(1, int(len(lines) * 0.1))

    def sample_with_max_duplicate(lines, size, max_duplicate_ratio=0.1):
        max_duplicates = max(1, int(size * max_duplicate_ratio))
        sampled_lines = []
        while len(sampled_lines) < size:

            new_samples = random.choices(lines, k=size - len(sampled_lines))
            sampled_lines.extend(new_samples)

            counter = Counter(sampled_lines)
            most_common_count = counter.most_common(1)[0][1] if counter else 0
            if most_common_count > max_duplicates:

                sampled_lines = []
                for item, count in counter.items():
                    sampled_lines.extend([item] * min(count, max_duplicates))
                if len(sampled_lines) > size:
                    sampled_lines = random.sample(sampled_lines, size)
        return sampled_lines[:size]

    if mode == 1:

        valid_lines = sample_with_max_duplicate(lines, valid_size, max_duplicate_ratio=0.1)
        with open(os.path.join(output_dir, "valid.jsonl"), "w", encoding="utf-8") as valid_out:
            for line in valid_lines:
                json.dump({"text": line}, valid_out, ensure_ascii=False)
                valid_out.write("\n")
        print(f"✅ Done: {len(lines)} train + {len(valid_lines)} valid samples")

    elif mode == 2:

        valid_lines = random.sample(lines, min(valid_size, len(lines)))
        with open(os.path.join(output_dir, "valid.jsonl"), "w", encoding="utf-8") as valid_out:
            for line in valid_lines:
                json.dump({"text": line}, valid_out, ensure_ascii=False)
                valid_out.write("\n")
        print(f"✅ Done: {len(lines)} train + {len(valid_lines)} valid samples")

    elif mode == 3:

        valid_lines = sample_with_max_duplicate(lines, valid_size, max_duplicate_ratio=0.1)
        test_lines = sample_with_max_duplicate(lines, valid_size, max_duplicate_ratio=0.1)
        with open(os.path.join(output_dir, "valid.jsonl"), "w", encoding="utf-8") as valid_out:
            for line in valid_lines:
                json.dump({"text": line}, valid_out, ensure_ascii=False)
                valid_out.write("\n")
        with open(os.path.join(output_dir, "test.jsonl"), "w", encoding="utf-8") as test_out:
            for line in test_lines:
                json.dump({"text": line}, test_out, ensure_ascii=False)
                test_out.write("\n")
        print(f"✅ Done: {len(lines)} train + {len(valid_lines)} valid + {len(test_lines)} test samples")

    elif mode == 4:

        valid_size = min(valid_size, len(lines))
        valid_lines = random.sample(lines, valid_size)
        remaining_lines = [line for line in lines if line not in valid_lines]
        test_size = max(1, int(len(lines) * 0.1)) if remaining_lines else 0
        test_size = min(test_size, len(remaining_lines))
        test_lines = random.sample(remaining_lines, test_size) if remaining_lines else []
        with open(os.path.join(output_dir, "valid.jsonl"), "w", encoding="utf-8") as valid_out:
            for line in valid_lines:
                json.dump({"text": line}, valid_out, ensure_ascii=False)
                valid_out.write("\n")
        with open(os.path.join(output_dir, "test.jsonl"), "w", encoding="utf-8") as test_out:
            for line in test_lines:
                json.dump({"text": line}, test_out, ensure_ascii=False)
                test_out.write("\n")
        print(f"✅ Done: {len(lines)} train + {len(valid_lines)} valid + {len(test_lines)} test samples")

def main():
    input_file = "001.txt"
    output_dir = "data"

    print("\n📋 txt → jsonl Text Splitting:")
    print("(All content is split during conversion, blank lines are not counted)")
    print("1 - All content is written to train.jsonl; 10% of the random lines are written to valid.jsonl (duplication lines ≤ 10%); test.jsonl is not generated.")
    print("2 - All content is written to train.jsonl; 10% of the random lines are written to valid.jsonl (no duplication allowed); test.jsonl is not generated")
    print("3 - All content is written to train.jsonl; 10% of the random lines are written to valid.jsonl (duplication lines is ≤ 10%); 10% of the random lines are written to test.jsonl (duplication lines is ≤ 10%)")
    print("4 - All content is written to train.jsonl; 10% of the random lines are written to valid.jsonl (no duplication allowed); 10% of the random lines are written to test.jsonl (no duplication allowed)")

    while True:
        try:
            choice = int(input("\nPlease enter options (1-4): "))
            if choice in [1, 2, 3, 4]:
                break
            else:
                print("❌ Please enter a number between 1 and 4")
        except ValueError:
            print("❌ Please enter a valid number")

    random.seed(42)

    create_jsonl_files(input_file, output_dir, choice)

if __name__ == "__main__":
    main()
```
