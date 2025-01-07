# bigdata-save-parquet
Save DataFrame to Parquet format

```python
import getpass

USER = getpass.getuser()
print("User =", USER)

DOWNLOAD_BASE_PATH = f"/user/{USER}/notebooks/downloads"

def save_parquet(df: DataFrame, folder_name: str):
    folder_to_download = f"{DOWNLOAD_BASE_PATH}/{folder_name}"
    
    print(f"Working at {folder_to_download}...")

    !hdfs dfs -rm -r -f -skipTrash {folder_to_download}/*
    
    df.write.mode('overwrite').parquet(folder_to_download)
    
    !hdfs dfs -copyToLocal {folder_to_download} {folder_name}
    !tar cvzf {folder_name}.tar.gz {folder_name}/*
    !rm -rf {folder_name}
    !hdfs dfs -copyFromLocal {folder_name}.tar.gz {folder_to_download}
    !hdfs dfs -rm -r -f -skipTrash {folder_to_download}/*.parquet
    !rm {folder_name}.tar.gz
    !hdfs dfs -ls {folder_to_download}

    print(f"{folder_name}.tar.gz file available to download at {folder_to_download}")
```
