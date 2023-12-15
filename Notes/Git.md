#### 一、已经标记为追踪的文件忽略本地修改

```shell
本地临时修改某些配置文件或者逻辑代码，但是又不想传到云端时，可以使用以下命令忽略本次修改。这样commit时文件不会被检测到
update-index --assume-unchanged  <filepath>
```