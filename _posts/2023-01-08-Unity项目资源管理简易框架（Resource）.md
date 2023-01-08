---
title: Unity项目资源管理框架（Resource）
description: Resource文件夹下的资源加载管理器，加载方式采用同步加载
categories:
- Unity框架
tags:
- Unity框架
- 资源管理
---

## 背景

在Unity的轻量型项目中如果对优化没有特别搞得要求，如果需要用到Resource.Load这个接口来对资源进行加载，且当模型音效UI等资源繁杂需要对其进行管理时，便可能需要一个Resource文件夹下的资源加载管理器用于对资源进行统一管理。

## 思路概述

按照资源的类型在Resource中建文件夹，在管理器中对文件夹的路径进行统一保存，对已加载的资源用一个字典进行统一保存来对所加载资源的内存和路径等信息进行统一管理，另外实现一些对字典的特定操作方法即可实现对资源加载的管理。

## 具体实现代码及其思路梳理

### 资源路径

确定有哪些资源，对资源的类型进行分类，对此建立一个枚举类型方便后期加载资源时的区分。

```csharp
public enum AssetsEnum
{
    //模型
    Model,
    //音效
    Audio,
    //界面
    UIPrefab,
    //icon
    Icon,
    //背景图
    TexBg,
}
```

对不同的资源类型在Resource文件夹下分别建立各自的文件夹，并将其路径进行静态存储

```csharp
public static readonly string ModelRootPath = "Model/";
public static readonly string AudioRootPath = "Audio/";
public static readonly string UIRootPath = "Prefabs/UI/";
public static readonly string IconRootPath = "Texture/Icon/";
public static readonly string TexBgRootPath = "Texture/TexBg/";
```

为方便生成对应资源的路径信息，可以封装一个方法，当调用时只需要传入名字和资源类型即可

```csharp
static string GetResPath(string assetName, AssetsEnum assetsEnum)
{
    string path = "";
    switch (assetsEnum)
    {
        case AssetsEnum.Model:
            path = ModelRootPath + assetName;
            break;
        case AssetsEnum.Audio:
            path = AudioRootPath + assetName;
            break;
        case AssetsEnum.UIPrefab:
            path = UIRootPath + assetName;
            break;
        case AssetsEnum.Icon:
            path = IconRootPath + assetName;
            break;
        case AssetsEnum.TexBg:
            path = TexBgRootPath + assetName;
            break;
    }
    return path;
}
```

### 资源管理

对于已经加载的资源，将他们的路径、名字和资源一起封装成一个类，将他们存入一个列表来进行管理。

```csharp
public static List<AssetDataInfo> dicAllAsset = new List<AssetDataInfo>();
public class AssetDataInfo
{
    public string assetName;
    public AssetsEnum assetType;
    public Object assetObj;
}
```

### 资源加载

到此我们已经可以获取到资源的路径并已经有了一个列表可以来对加载资源数据信息进行存储和管理，下面来写资源的加载，这里记录的是同步加载资源的两种方式。

```csharp
publicstatic T LoadResAsset<T>(string assetName, AssetsEnum assetsEnum) where T : Object
{
    AssetDataInfo fdata = dicAllAsset.Find(x => x.assetName == assetName);
    if (fdata != null)
    {
        return (T)fdata.assetObj;
    }
    string path = GetResPath(assetName, assetsEnum) ;
   
    T loadAsset = Resources.Load<T>(path);
    //无效资源判断,null 或值类型判断
    if (loadAsset == default(T))
    {
        Debug.LogWarning("加载的资源不存在assetName=" + assetName);
        return default(T);
    }
    AssetDataInfo dataInfo = new AssetDataInfo();
    dataInfo.assetName = assetName;
    dataInfo.assetType = assetsEnum;
    dataInfo.assetObj = loadAsset;
    dicAllAsset.Add(dataInfo);
    return loadAsset;
}
public static Object LoadResAsset(string assetName, AssetsEnum assetsEnum, System.Type systemTypeInstance = null)
{
    Object asset = null;
    AssetDataInfo fdata = dicAllAsset.Find(x => x.assetName == assetName);
    if (fdata != null)
    {
        return fdata.assetObj;
    }
    string path = GetResPath(assetName, assetsEnum);
    if (systemTypeInstance == null)
    {
        asset = Resources.Load(path);
    }
    else
    {
        asset = Resources.Load(path, systemTypeInstance);
    }

    if (asset != null)
    {
        AssetDataInfo dataInfo = new AssetDataInfo();
        dataInfo.assetName = assetName;
        dataInfo.assetType = assetsEnum;
        dataInfo.assetObj = asset;
        dicAllAsset.Add(dataInfo);
    }
    else
    {
        Debug.LogWarning("加载的资源为空assetName=" + assetName);
    }
    return asset;
}
```

### 资源管理

在已经得到了一个资源列表的情况下，提前写一些对列表操作的方法，如资源的清理卸载或查找等，便可在其他的代码中对资源进行管理

```csharp
//获取所有的图片
public static Sprite[] GetAllSprite(string assetName, AssetsEnum assetsEnum)
{
    string path = GetResPath(assetName, assetsEnum);
    Sprite[] allSp = Resources.LoadAll<Sprite>(path);
    return allSp;
}
//获取资源路径
static string GetResPath(string assetName, AssetsEnum assetsEnum)
{
    string path = "";
    switch (assetsEnum)
    {
        case AssetsEnum.Model:
            path = ModelRootPath + assetName;
            break;
        case AssetsEnum.Audio:
            path = AudioRootPath + assetName;
            break;
        case AssetsEnum.UIPrefab:
            path = UIRootPath + assetName;
            break;
        case AssetsEnum.Icon:
            path = IconRootPath + assetName;
            break;
        case AssetsEnum.TexBg:
            path = TexBgRootPath + assetName;
            break;
    }
    return path;
}
//销毁对象
public static void DestroyObj(GameObject obj, bool bImmediate = false)
{
    if (null != obj)
    {
        if (false == bImmediate)
        {
            GameObject.Destroy(obj);
        }
        else
        {
            GameObject.DestroyImmediate(obj);
        }
    }
}
//清理某资源
public static void ClearAsset(string assetName)
{
    AssetDataInfo fdata = dicAllAsset.Find(x => x.assetName == assetName);
    if (fdata != null)
    {
        dicAllAsset.Remove(fdata);
    }
}
//清理并卸载对应类型的资源
public static void ClearAsset(AssetsEnum etype)
{
    for (int i = dicAllAsset.Count-1; i >0; i--)
    {
        if (dicAllAsset[i].assetType==etype)
        {
            dicAllAsset.RemoveAt(i);
        }
    }
    Resources.UnloadUnusedAssets();
}
//清理并卸载所有资源
public static void ClearAllAsset()
{
    dicAllAsset.Clear();
    Resources.UnloadUnusedAssets();
}
//获取已加载的资源数量
public static int GetAssetCount()
{
    return dicAllAsset.Count;
}
```

## 说明

Resource文件夹上限为两个G左右，适合小项目或Demo使用，通常并不作为资源加载的选择。后续将整理通过AB包加载资源的方法及相应管理框架。
