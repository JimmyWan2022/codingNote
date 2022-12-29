## .NET(C#) AutoMapper使用扩展方法对record类型映射



C# 9 引入record，它一种可以创建的新引用类型，而不是类或结构。AutoMapper映射此类存在问题，则可以使用扩展方法进行映身。



## 1、 record定义及使用

**参考文档**：[.NET C# 9.0 record和with的定义及使用](https://www.cjavapy.com/article/2484/)

## 2、扩展方法

**参考文档**：[.NET(C#) 扩展方法(Extension)](https://www.cjavapy.com/article/2483/)

实现代码如下，

```c#
public record BFrom 
{
    public Guid Id { get; init; }
    public Guid DbExtraId { get; init; }
}
public static class AutoMapperExtensions
{
    public static IMappingExpression<TSource, TDestination> MapRecordMember<TSource, TDestination, TMember>(
        this IMappingExpression<TSource, TDestination> mappingExpression,
        Expression<Func<TDestination, TMember>> destinationMember, Expression<Func<TSource, TMember>> sourceMember)
    {
        var memberInfo = ReflectionHelper.FindProperty(destinationMember);
        string memberName = memberInfo.Name;
        return mappingExpression
            .ForMember(destinationMember, opt => opt.MapFrom(sourceMember))
            .ForCtorParam(memberName, opt => opt.MapFrom(sourceMember));
    }
}
```

**调用方法：**

```c#
CreateMap<BFrom, ATo>()
    .MapRecordMember(a => a.ExtraId, src => src.DbExtraId)
    .ReverseMap();
```