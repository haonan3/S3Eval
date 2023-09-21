# SQLEval: A diagnositic benchmark for Large Language Model

# 🔥 Updates

- [**2023-9-30**]: We released our [code](https://github.com/lfy79001/SQLEval)

# 🔧 Task
<插入一张任务示例图>

The benchmark have two main tasks: __SQL execution__ and __SQL language insturction__.


# ✨ Features
SQLEval is a platform for Large Language Model Evaluation (especially for LLM diagnositic). It has the following features:
- **Reasoning**: SQL contains a rich grammar in which each keyword implies a different reasoning capability, and SQLEval can effectively test model reasoning capabilities.
- **Long-Context Understanding**: The difficulty of long text evaluation is how to collect meaningful texts and tasks, our work can theoretically evaluate any long-context capability of any LLM context windows length. 
- **Controllable Analysis**: This platform allows fine-grained control of data generation. Such as detailed attributes of the table, fine-grained difficulty control of the generated SQL, and so on. Users have the flexibility to use it to explore more features of LLM.
- **Dynamic without data leakage**: Randomly construct synthetic data that has never been seen by LLMs, which greatly alleviates data leakage problem in LLM evaluation.


# ⚙️ Start

## 
```bash
bash run.sh
```

```bash
# parameter introduction
python synthetic.py \
  --db_path ./db/db1 \  # Location of the generated tables
  --new_db True \   # True: create new tables in this db_path, then generate data. False: use existing tables to generate data
  --total_number 1000 \   # How many training data do you want to generate
  --each_table_number  50 \  # How much training data do you want to generate on one table
  --database_config ./config/database_config.json \ # Fine-grained config for form properties
  --sql_config ./config/sql_config.json \ #   # The most important file, SQL Config File
  --synthetic_mode general \ # general: Standard diverse SQL generation methods(recommended). template: Custom SQL template generation.
  --template  ./template/general.json  \  # SQL grammar/template location
  --data_mode eval \ # data format styles, 'eval' is more commonly used. Changes are not recommended.
```

If you want to change the database config
```bash
// database_config.json
{
  "col_min": 5, // the min number of cols
  "col_max": 8, // the max number of cols
  "row_min": 15,  // the min number of rows
  "row_max": 40,  // the max number of rows
  "text_int_date": [0.55, 0.35, 0.1]  // text,int,date  header ratio
}
```

If you want to change the sql config
```bash
{
  "nest": [1],  // Number of SQL nestings. options: [1], [2], [1,2],[1,2, 3]
  "keywords_setting": { // if a Keyword is False, then no SQL containing this Keyword is generated.
    "select": true,      
    "where": true,
    "group by": true,
    "having": true,
    "order by": true
  },
  "length_setting": {      // control the length of sql
    "is_available": false, // To enable this setting, you need to adjust "is_available" to true first.
    "value": [],           // 'value' can be set to specific values, such as [13,14,15], if value is null, then the range is used [min, max]
    "min": 6,
    "max": 16
  },
  "column_ratio": {        // Controlling the ratio of columns involved in SQL
    "is_available": false,  // To enable this setting, you need to adjust "is_available" to true first.
    "value": [],            // 'value' can be set to specific values, such as [1,2], Control the number of columns involved in SQL
    "min": 0.1,             // if value is null, then the range is used [min, max], it's the used ratio = (used columns) / (all columns)
    "max": 0.3
  },
  "select_row_ratio":{     // Controlling the ratio of rows involved in select keyword
    "is_available": false,  // To enable this setting, you need to adjust "is_available" to true first.
    "value": [],            // 'value' can be set to specific values, such as [1,2,3,4], Control the number of rows involved in SQL
    "min": 0.1,             // if value is null, then the range is used [min, max], it's the used ratio = (select rows) / (all rows)
    "max": 0.2
  },
  "calculate_times": {   // Controlling the calculate times of the sql ['+','-','*','/','sum','count','min','max','avg']
    "is_available": false,   // To enable this setting, you need to adjust "is_available" to true first.
    "value": [1,2,3,4]      // 'value' can be set to specific values, means the calculate times
  },
  "filter_times": {  // Controlling the filter times of the sql ['=','>','<','in','like']
    "is_available": false,   // To enable this setting, you need to adjust "is_available" to true first.
    "value": [1,2,3,4,5]  // 'value' can be set to specific values, means the calculate times
  },
  "answer_location": {    // Controlling the location of answer in the table, usually used in long-context understanding
    "is_available": false,   // To enable this setting, you need to adjust "is_available" to true first.
    "value": null,          
    "min": 0.1,             // if value is null, then the range is used [min, max], means that  0.1 <  (Row where answer is located ) / (Row number) < 0.9
    "max": 0.9              
  },
  "answer_cells_number": 1,  // usually remains 1 in this repo, we often just test the sql whose answer is from one cell.
  "include": [],
  "exclude": []
}
```

Template or General

`general` mode is recommended to use, cause it has more grammar. You only need to control the config, or simply control the file.

`template` mode can also be used, but this template is slightly more difficult to write. can be used when testing long-context, see template template/long.txt




