废话不说，直接上代码：
方案一：
<pre>

divStrArry () {
    str=$@;
    OLD_IFS="$IFS"
    IFS="/"
    arr=($str)
    IFS="$OLD_IFS"
    num=${#arr[@]}
    for i in ${arr[@]}
    do
      echo $i
    done
}
</pre>
方案二：

<code>
divStrArry () {
    str="$@";
    set -A arr $(echo $str|tr '/' ' '|tr -s ' ')
    num=${#arr[@]}
    for i in ${arr[@]}
    do
      echo "$i"
    done
}
<code>

那么有一个需求：使用shell脚本FTP 建立多级目录：包括子目录。不支持mkdir -p的。
同样直接上代码
<pre>
funnc() {
    local r
    local a
    r="$@"
    while [[ "$r" != "$a" ]] ; do
        a=${r%%/*}
        echo "mkdir $a"
        echo "cd $a"
        r=${r#*/}
    done
}
ftp -inv 0.0.0.0 < < EOF
user username pass
bin
$(funnc Misc/Sub/acct/${host_date})
mput xw.TXT
bye
EOF
</pre>
其它废话也不说了…