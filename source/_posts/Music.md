---
title: 网易缓存提取歌曲
tags: [技巧,编程,Python]
categories: 编程
cover: "/covers/WY.jpg"
---
## 异或缓存从而反译出能直接听的MP3文件
### 只是让歌曲在会员过期后仍然可以听，甚至离线听

``` python
import os
from datetime import datetime

# 定义要读取的文件夹路径和输出文件夹路径
input_folder_path = 'D:/Software Data/CloudMusic/Cache'
output_folder_path = 'D:/Download'

# 获取文件夹中所有 .uc 后缀文件的路径、大小和日期
file_info_list = []
for file in os.listdir(input_folder_path):
    if file.endswith('.uc'):
        file_path = os.path.join(input_folder_path, file)
        file_size = os.path.getsize(file_path)
        file_time = os.path.getmtime(file_path)
        file_date = datetime.fromtimestamp(file_time).strftime('%Y-%m-%d %H:%M:%S')
        file_info_list.append((file_path, file_size, file_time))

# 对文件信息列表进行排序，先按日期降序排列
sorted_file_info_list = sorted(file_info_list, key=lambda x: -x[2])

# 打印排序好的文件列表，让用户选择要执行后续操作的文件
for index, file_info in enumerate(sorted_file_info_list):
    print(f'{index+1}. {os.path.basename(file_info[0])} ({file_info[1]} bytes, {datetime.fromtimestamp(file_info[2]).strftime("%Y-%m-%d %H:%M:%S")})')

# 让用户选择要执行后续操作的文件
selected_file_index = int(input('请选择一个要处理的文件：')) - 1
selected_file_path = sorted_file_info_list[selected_file_index][0]

# 获取新文件名
new_file_name = input('请输入新文件名：')

# 读取选中文件的内容，并进行异或操作和数据转换
with open(selected_file_path, 'rb') as f:
    data = f.read()
    processed_data = bytearray([b ^ 0xA3 for b in data])

# 将处理后的内容保存为新文件并更改后缀名为 .mp3
new_file_name = os.path.splitext(new_file_name)[0] + '.mp3'
new_file_path = os.path.join(output_folder_path, new_file_name)
with open(new_file_path, 'wb') as f:
    f.write(processed_data)

# 修改文件名
os.rename(new_file_path, os.path.join(output_folder_path, new_file_name))
```

可以通过bat运行...

```
python -i D:\20947\OneDrive - MSFT\文档\VsCode\Python\缓存转换\main.py
```