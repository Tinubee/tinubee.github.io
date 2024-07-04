---
title: EntityFrameWork + PostgreSQL 기본 사용 방법
description: EntityFrameWork + PostgreSQL 기본 사용 방법
date: 2024-05-15 21:32:00 +0800
categories: [Note,PostgreSQL]
tags: [postgresql]
pin: true
math: true
mermaid: true
#image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---
# EntityFrameWork + PostgreSQL 사용하기.

### 1. 프로젝트 생성 및 nuget패키지에서 다운로드 해야함(버전은 .Net framework 8.2 기준으로 3.1.18받으면 됨)
- Npgsql.EntityFrameworkCore.PostgreSQL
- Microsoft.EntityFrameworkCore.Tools


### 2. 프로젝트에 Models 폴더 만들고 Data폴더 만들기(통상적으로)


### 3. Models에 db에 table로 사용할 class들 생성하기(아래는 예시이며 각각은 따로 파일 만들어서 생성하는 게 좋음)

```cs
Customer.cs
----------------------------------------------------------------------------------
namespace ContosoPizza.Models
{
    public class Customer
    {
        public int Id { get; set; }

        public string FirstName { get; set; } = null;
        public string LastName { get; set; } = null;
        public string Address { get; set; }
        public string Phonne { get; set; }
        public ICollection<Order> Orders { get; set; } = null;

    }
}
```

```cs
Order.cs
----------------------------------------------------------------------------------
namespace ContosoPizza.Models
{
    public class Order
    {

        public int Id { get; set; }

        public DateTime OrderPlaced { get; set; }

        public DateTime OrderFulfilld { get; set; }

        public int CustomerId { get; set; }

        public Customer Customer { get; set; } = null;

        public ICollection<OrderDetail> OrderDetails { get; set; } = null;


    }
}
```

```cs
OrderDetail.cs
----------------------------------------------------------------------------------
namespace ContosoPizza.Models
{
    public class OrderDetail
    {

        public int Id { get; set; }
        public int Quantity { get; set; }
        public int ProductionId { get; set; }
        public int OrderId { get; set; }
        public Order Order { get; set; } = null;
        public Product Product { get; set; } = null;
    }
}

Produict.cs
----------------------------------------------------------------------------------
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace ContosoPizza.Models
{
    public class Product
    {

        [Key]
        public int Id { get; set; }

        public string Name { get; set; } = null;

        [Column(TypeName = "decimal(6, 2)")]
        public decimal Price { get; set; }

    }
}
```

----------------------------------------------------------------------------------

### 4. Data 폴더에 아래와 같이 Context 클래스 생성하기
- Connection 안에 들어가는 내용은 DB에 맞게 수정하기, DataBase 저장소는 미리 만들어 놓는다.

```cs
ContosoPizzaContxt.cs
----------------------------------------------------------------------------------
using ContosoPizza.Models;

using Microsoft.EntityFrameworkCore;
using Npgsql;

namespace ContosoPizza.Data
{
    public class ContosoPizzaContext : DbContext
    {
        public DbSet<Customer> Customers { get; set; } = null;

        public DbSet<Order> Orders { get; set; } = null;

        public DbSet<Product> Products { get; set; } = null;

        public DbSet<OrderDetail> OrderDetails { get; set; } = null;

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            NpgsqlConnectionStringBuilder b = new NpgsqlConnectionStringBuilder() { Host = "localhost", Port = 5432, Username = "postgres", Password = "password", Database = "Database Name" };
            
            optionsBuilder.UseNpgsql(b.ConnectionString);
        }
    }
}
```

### 5. 패키지 관리자 콘설 켜기
- 도구 -> Neget패키지관리 -> 패키지 관리자 콘설 클릭

### 6. Add-Migration InitialCreate 입력(Add-Migration 뒤는 임의 변경 가능)
- 제대로 완료 되면 아래와 같이 나오고 Migrations 폴더가 생김 
>Build started... <br>
Build succeeded.

### 7. Update-Database 입력
- 제대로 완료되면 아래와 같이 나오고 설치되어 있는 PgAdmin에 접속해서 table View All 해서 확인해보면 table 생성된 것 알 수 있음

>Build started...
<br>Build succeeded.
<br>Applying migration '20231205030201_InitialCreate'.
<br>Done.

### 8-1. 혹시나 빠진 항목이 있다면 추가하기(Customer에 Email이 빠졌을 경우 가정)
```cs
    public class Customer
    {
        public int Id { get; set; }

        public string FirstName { get; set; } = null;
        public string LastName { get; set; } = null;
        public string Address { get; set; }
        public string Phone { get; set; }
        public string Email { get; set; }
        public ICollection<Order> Orders { get; set; } = null;

    }
```

### 8-2. 패키지 관리자 콘솔에 Add-Migration AddEmail 이라고 입력
- 여기서 AddEmail은 자유자재로 변경 가능 자유롭게 이름지정 가능

### 8-3. Update-Database 입력해서 적용하면 완료!

---

# ✅ program.cs에서 아래와 같이 기본기 연습해보기.

### Product 생성 및 변화 저장

```cs
ContosoPizzaContext context = new ContosoPizzaContext();
            
Product veggieSpectial = new Product()
{
    Name = "Veggi LHD good",
    Price = 9.99M
};

context.Products.Add(veggieSpectial);

Product deluxeMeat = new Product()
{
    Name = "DMP goods",
    Price = 12.99M
};
context.Add(deluxeMeat);

context.SaveChanges();
```

### product 검색해서 console에 뿌려보기(fluent API를 사용해서 검색하는 방법, Linq 이용은 다음에)

```cs
ContosoPizzaContext context = new ContosoPizzaContext();
var products = context.Products.Where(p => p.Price > 10.00M).OrderBy(p => p.Name);

foreach ( var product in products )
{
    Console.WriteLine($"Id:      {product.Id}");
    Console.WriteLine($"Name:    {product.Name}");
    Console.WriteLine($"Price:   {product.Price}");
    Console.WriteLine(new string('-',20));
}
```

### product 내용 바꾸고 update 해보기(price를 10.99 로 바꾸고 저장)

```cs
ContosoPizzaContext context = new ContosoPizzaContext();
var veggiSpectial = context.Products.Where(p => p.Name == "Veggi LHD good").FirstOrDefault();

if(veggiSpectial is Product)
{
    veggiSpectial.Price = 10.99M;
}

context.SaveChanges();

var products = context.Products.Where(p => p.Price > 10.00M).OrderBy(p => p.Name);

foreach ( var product in products )
{
    Console.WriteLine($"Id:      {product.Id}");
    Console.WriteLine($"Name:    {product.Name}");
    Console.WriteLine($"Price:   {product.Price}");
    Console.WriteLine(new string('-',20));
}
```

### product 내용을 삭제하고 update 해보기

```cs
ContosoPizzaContext context = new ContosoPizzaContext();
var veggiSpectial = context.Products.Where(p => p.Name == "Veggi LHD good").FirstOrDefault();

if(veggiSpectial is Product)
{
    //veggiSpectial.Price = 10.99M;
    context.Remove(veggiSpectial);
}

context.SaveChanges();

var products = context.Products.Where(p => p.Price > 10.00M).OrderBy(p => p.Name);

foreach ( var product in products )
{
    Console.WriteLine($"Id:      {product.Id}");
    Console.WriteLine($"Name:    {product.Name}");
    Console.WriteLine($"Price:   {product.Price}");
    Console.WriteLine(new string('-',20));
}
```
---


# 10. 부록
[MS에서 제공하는 참조 동영상](https://www.youtube.com/watch?v=SryQxUeChMc&list=PLdo4fOcmZ0oX7uTkjYwvCJDG2qhcSzwZ6)
(Getting Started with Entity Framework Core [1 of 5] | Entity Framework Core for Beginners)


> **디자인 패키지 ~~ 발생 시 해결방안** 
> - 구성관리자에서 디버깅모드를 remote가 아니라 `local`로 두고 해보기.
> - 해당 컨텍스트 내에 `OnConfiguring도 추가`해서 따로 연결시켜주기. host만 대상 PC IP 입력.
> - 명령어에 `Add-Migration Initial블라블라 -OutputDir "OutputDir" -Context "logtable"` 입력.
> - `Update-DataBase` 도 마찬가지로 입력.


