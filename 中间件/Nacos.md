# Springboot集成Nacos服务发现

```
package com.coocaa.websocket.api;

import com.alibaba.nacos.api.annotation.NacosInjected;
import com.alibaba.nacos.api.exception.NacosException;
import com.alibaba.nacos.api.naming.NamingService;
import com.alibaba.nacos.api.naming.pojo.Instance;
import com.alibaba.nacos.spring.context.annotation.discovery.EnableNacosDiscovery;
import com.coocaa.websocket.api.util.IpUtil;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;

import javax.annotation.PostConstruct;
import java.util.List;

@Configuration
@EnableNacosDiscovery
public class NacosRegisterConfiguration {
    @NacosInjected
    NamingService namingService;

    @Value("${spring.application.name}")
    String applicationName;

    @Value("${project.http.port}")
    Integer serverPort;

    @PostConstruct
    public void registerInstance() throws NacosException {
        namingService.registerInstance(applicationName, IpUtil.getIp(), serverPort);
        List<Instance> allInstances = namingService.getAllInstances(applicationName);
        System.out.println(allInstances);
    }

}

```



```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>nacos-discovery-spring-boot-starter</artifactId>
            <version>0.2.7</version>
        </dependency>
     </dependencies>
```


nacos缺点

1. 兼容性差，文档不全，springboot和nacos版本对应信息，导致一直试错去找能匹配的版本。
2. 更新慢，高版本的springboot经常不支持

