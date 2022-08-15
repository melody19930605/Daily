# 日常积累

---

## ***pip临时换源***

pip install requests -i http://pypi.douban.com/simple/ [--trusted-host pypi.douban.com]

## ***npm临时换源***

npm install 软件名 --registry https://registry.npm.taobao.org

## ***ssh连接失败可能存在的解决办法***

ssh-keygen -R 服务器ip地址

## ***Docker创建Niginx，并附带配置文件***

mkdir -p /home/nginx/conf
mkdir -p /home/nginx/log
mkdir -p /home/nginx/html

# 生成容器
docker run --name nginx -p 9001:80 -d nginx
# 将容器nginx.conf文件复制到宿主机
docker cp nginx:/etc/nginx/nginx.conf /home/nginx/conf/nginx.conf
# 将容器conf.d文件夹下内容复制到宿主机
docker cp nginx:/etc/nginx/conf.d /home/nginx/conf/conf.d
# 将容器中的html文件夹复制到宿主机
docker cp nginx:/usr/share/nginx/html /home/nginx/

sudo docker run \
-p 80:80 \
--name nginx \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/conf/conf.d:/etc/nginx/conf.d \
-v /home/nginx/log:/var/log/nginx \
-v /home/nginx/html:/usr/share/nginx/html \
-d nginx:latest

## ***使用Docker创建一个MySQL实例用来测试，宿主机或Docker实例删除后数据等全部丢失***

-p 宿主机端口：容器端口
docker run --name tms -p 5600:3306 -e MYSQL_ROOT_PASSWORD= -d mysql

redis
docker run -itd --name redisServer -p 6379:6379 redis --requirepass "password"

## ***windows开始测试模式***

bcdedit /set nointegritychecks o  
bcdedit /set testsigning on  
shutdown  -r -t 0

## ***Js在数组中删除某个对象***

```javascript
var itemIndex = $.inArray(v, target);
target.splice(itemIndex,1);
```

## ***C#代码片段***

```csharp
/// <summary>
/// 实体转换器
/// </summary>
public static class DefaultModelAdapter
{
    /// <summary>
    /// 实体转换器(指定列转换)
    /// </summary>
    /// <typeparam name="T">目标实体</typeparam>
    /// <typeparam name="TSource">源实体</typeparam>
    /// <param name="t">目标实体</param>
    /// <param name="source">源实体</param>
    /// <param name="express">指定需要将源实体的字段赋值给目标实体的字段(不传默认全部替换)</param>
    public static void Transfer<T, TSource>(T t, TSource source, params Expression<Func<T, object>>[] express)
    {
        // 源实体的所有字段
        var sourceValues = source.GetType().GetProperties();
        // 目标实体的所有字段
        var targetVaules = t.GetType().GetProperties();
        // 获取所有需要替换的字段名称
        var columnNames = express.Select(item => GetPropName(item.Body)).ToList();
        // 需要更新的键值对
        var updates = sourceValues.Where(pi => columnNames.Contains(pi.Name)).ToDictionary(pi => pi.Name, pi => pi.GetValue(source, null));
        // 更新
        foreach (var item in updates)
        {
            foreach (var pi in targetVaules)
            {
                if (pi.Name == item.Key)
                {
                    pi.SetValue(t, item.Value);
                }
            }
        }
    }

    /// <summary>
    /// 实体转换器(全部转换)
    /// </summary>
    /// <typeparam name="T">目标实体</typeparam>
    /// <typeparam name="TSource">源实体</typeparam>
    /// <param name="t">目标实体</param>
    /// <param name="source">源实体</param>
    public static void Transfer<T, TSource>(T t, TSource source)
    {
        // 源实体的所有字段
        var sourceValues = source.GetType().GetProperties();
        // 目标实体的所有字段
        var targetVaules = t.GetType().GetProperties();
        // 需要更新的键值对
        var updates = sourceValues.ToDictionary(pi => pi.Name, pi => pi.GetValue(source, null));
        // 更新
        foreach (var item in updates)
        {
            foreach (var pi in targetVaules)
            {
                if (pi.Name == item.Key)
                {
                    pi.SetValue(t, item.Value);
                }
            }
        }
    }

    /// <summary>
    /// Lambda表达式转换
    /// </summary>
    /// <param name="express"></param>
    /// <returns></returns>
    public static string GetPropName(Expression express)
    {
        var str = string.Empty;
        if (express is MemberExpression)
        {
            str = ((MemberExpression) express).Member.Name;
        }
        else if (express is UnaryExpression)
        {
            str = ((MemberExpression) ((UnaryExpression) express).Operand).Member.Name;
        }
        else if (express is ParameterExpression)
        {
            str = ((ParameterExpression) express).Type.Name;
        }
        return str;
    }
}
```
