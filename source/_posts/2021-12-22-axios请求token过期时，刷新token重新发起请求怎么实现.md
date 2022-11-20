---
title: axios请求token过期时，如何实现刷新token并重试该请求
date: 2021-12-22 19:11:03
tags:
- axios
categories: 
- tools
---

#### 问题
>当用户发起一个请求时，判断token是否已过期，若已过期则先调refresh更新token的接口，拿到新的token后再继续执行之前的所有请求。

>* 难点：当同时发起多个请求，token 过期会调用多次更新 token 接口；此时刷新token的接口还没返回，此时其他请求该如何处理，在刷新token接口返回后才能继续请求


#### 请求拦截，为axios请求添加token
```ts
import axios, { AxiosRequestConfig, AxiosResponse, AxiosError } from "axios";
const instance = axios.create({
    baseURL: "127.0.0.1：8080/gapi/", // 域名
    timeout: 30000,
    //当发送跨域请求时携带cookie
    withCredentials: false,
    httpsAgent: { rejectUnauthorized: false },
});
instance.interceptors.request.use((config: AxiosRequestConfig)=>{
    // 这里判断当前是否已登录状态
    if (window.localStorage.getItem("token") && window.localStorage.getItem("rtk")){
        // atk即为token在登录时已获取
        // 统一为请求添加token至headers
        config.headers["Authorization"] = "Bearer " + window.localStorage.getItem("token");
        return config;
    }else{
        // 用户未登录，或被挤下登录状态
        return Promise.reject(config);
    }
},(error: AxiosError) => {
    // 对请求错误做些什么
    return Promise.reject(error);
})
```
#### token过期时调用refresh更新token，并将当前接口暂存
响应拦截器中判断token过期，并调用refresh，并将当前请求暂存起来，等待token刷新成功之后，再重试请求
```ts
let isRefreshing = false; // 标记是否正在刷新token
let requests: any[] = []; // 存储待重发请求的数组
// 封装的刷新token的接口
const refresh = (options: Object) => {
    return axios.create({baseURL:"127.0.0.1：8080/gapi/",withCredentials: false}).post("/api/token/refresh", {}, options);
};
instance.interceptors.response.use((response: AxiosResponse)=>{
    // 对响应成功的数据做点什么
},error=>{
    // 判断接口token过期
    if(error.response?.status == 401 && config.url.indexOf("/api/token/refresh") == -1){
        // 调用续签接口
        let options = {
            headers: {
                rtk: window.localStorage.getItem("token"),
                tkt: window.localStorage.getItem("tkt"),
                "Content-Type": "application/x-www-form-urlencoded",
            },
        };
        // 是否正在刷新token
        if (!isRefreshing) {
            isRefreshing = true;
            // 调用刷新token接口
            return token_refresh(options)
                .then(async res => {
                    if (res.data.success) {
                        // 续签成功，更改缓存的token   同步请求
                        await window.localStorage.setItem("token", res.headers.token);
                        await window.localStorage.setItem("rtk", res.headers.tkt);
                        error.config.headers["Authorization"] = "Bearer" + window.localStorage.getItem("token");
                        // token刷新成功，将暂存请求列表内的请求依次重发
                        requests.forEach((cb: Function) => cb(res.headers.token));
                        requests = []; // 重新请求完清空
                        return instance(config);
                    } else {
                        // 续签失败---登录超时
                        // 处理token刷新失败的场景
                        // login_time_out(config.url); 
                    }
                })
                .catch(err => {
                            // 处理token刷新失败的场景
                    return Promise.reject(err);
                })
                .finally(() => {
                        isRefreshing = false;
                });
        }else{
            // token未在刷新中
            // 返回未执行 resolve 的 Promise--请求列表（他们等待续签完成之后执行）
            return new Promise(resolve => {
                // 用函数形式将 resolve 存入，等待刷新后再执行
                requests.push(token => {
                    error.config.headers.Authorization = `Bearer ${token}`;
                    // instance是上面创建的axios全局对象
                    resolve(instance(config));
                });
            });
        }
    }

    return Promise.reject(error);
})

```