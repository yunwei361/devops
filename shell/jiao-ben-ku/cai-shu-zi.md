# 猜数字

### guess.sh

猜一个1-1000以内的整数

```sh
#!/bin/bash

num_random=$(shuf -i 1-1000 -n 1)

while true
do
    read -p "Input a number: " num
    if [[ ! $num =~ ^[0-9]+$ ]]; then
        echo "Please input a integer :("
    else
        if [ $num -gt $num_random ]; then
            echo "Greater"
        elif [ $num -lt $num_random ]; then
            echo "Less"
        else
            echo "Good, you are right!"
            break
        fi
    fi
done
```
