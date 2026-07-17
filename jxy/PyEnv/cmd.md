导出虚拟环境的配置
```bash
#激活目标虚拟环境
conda activate 环境名
#导出到 yml 文件
conda env export > environment.yml
#若想去掉哈希 --最常用
conda env export --no-builds > environment.yml
#只导出 conda 安装的包（不含 pip）
conda list --explicit > spec-list.txt
#仅导出 pip 安装的第三方包
pip freeze > requirements.txt
```
根据配置重建虚拟环境
```bash
# yml文件里面的 prefix不会造成影响，可以修改yml里面的环境名实现自定义环境名称
conda env create -f environment.yml
conda activate 环境名
```