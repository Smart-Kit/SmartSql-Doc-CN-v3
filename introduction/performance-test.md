# 性能评测

``` ini

BenchmarkDotNet=v0.10.14, OS=Windows 10.0.16299.431 (1709/FallCreatorsUpdate/Redstone3)
Intel Core i7-4710MQ CPU 2.50GHz (Haswell), 1 CPU, 8 logical and 4 physical cores
Frequency=2435768 Hz, Resolution=410.5481 ns, Timer=TSC
.NET Core SDK=2.1.201
  [Host]     : .NET Core 2.0.7 (CoreCLR 4.6.26328.01, CoreFX 4.6.26403.03), 64bit RyuJIT
  DefaultJob : .NET Core 2.0.7 (CoreCLR 4.6.26328.01, CoreFX 4.6.26403.03), 64bit RyuJIT

```

|            ORM |                     Type |                  Method |        Return |     Mean |     Error |    StdDev | Rank |      Gen 0 |     Gen 1 |     Gen 2 | Allocated |
|--------------- |------------------------- |------------------------ |-------------- |---------:|----------:|----------:|-----:|-----------:|----------:|----------:|----------:|
|       SmartSql |       SmartSqlBenchmarks |                   Query | IEnumerable`1 | 101.6 ms | 0.2226 ms | 0.1738 ms |    1 |  2437.5000 | 1062.5000 |  375.0000 |  13.37 MB |
|         Native |         NativeBenchmarks | Query_IsDBNull_GetValue | IEnumerable`1 | 101.7 ms | 0.4101 ms | 0.3635 ms |    1 |  2437.5000 | 1062.5000 |  375.0000 |  13.37 MB |
|         Dapper |         DapperBenchmarks |                   Query | IEnumerable`1 | 104.4 ms | 1.3195 ms | 1.2342 ms |    2 |  3375.0000 | 1375.0000 |  625.0000 |  17.64 MB |
| SmartSqlDapper | SmartSqlDapperBenchmarks |                   Query | IEnumerable`1 | 105.7 ms | 1.1697 ms | 1.0941 ms |    3 |  3750.0000 | 1437.5000 |  625.0000 |  19.47 MB |
|         Native |         NativeBenchmarks |   Query_GetValue_DbNull | IEnumerable`1 | 107.4 ms | 1.0710 ms | 1.0018 ms |    4 |  3062.5000 | 1187.5000 |  500.0000 |  16.42 MB |
|       SqlSugar |       SqlSugarBenchmarks |                   Query | IEnumerable`1 | 108.9 ms | 0.4048 ms | 0.3787 ms |    5 |  2375.0000 | 1000.0000 |  312.5000 |  13.09 MB |
|             EF |             EFBenchmarks |                SqlQuery | IEnumerable`1 | 110.9 ms | 0.6922 ms | 0.6475 ms |    6 | 11062.5000 |         - |         - |  34.13 MB |
|          Chloe |          ChloeBenchmarks |                   Query | IEnumerable`1 | 114.5 ms | 2.2600 ms | 5.3711 ms |    7 |  2375.0000 | 1000.0000 |  312.5000 |  13.07 MB |
|             EF |             EFBenchmarks |        Query_NoTracking | IEnumerable`1 | 126.4 ms | 1.3197 ms | 1.2344 ms |    8 |  5937.5000 | 2250.0000 | 1062.5000 |  30.16 MB |
|             EF |             EFBenchmarks |     SqlQuery_NoTracking | IEnumerable`1 | 148.6 ms | 0.8290 ms | 0.7755 ms |    9 |  7437.5000 | 2937.5000 | 1250.0000 |  37.79 MB |
