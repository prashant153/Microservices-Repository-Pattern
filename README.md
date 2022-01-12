# Create Repository in Microservices

## Step 1. Create Model -> Domain -> Models  

Diary.cs 

## Step 2. Create DTO -> Domain -> Dtos 

DiaryDto.cs 

## Step 3. Add Virtual DbSet<Model>  in MyDBContext.cs -> Data -> DbContext 
``` 
public virtual DbSet<Diary> Diaries { get; set; }  
```

## Step 4. Add model builder in the above file: 
```
modelBuilder.Entity<Diary>(entity =>  { 

                entity.ToTable("Diary"); 

                entity.HasIndex(e => e.Id) 
                    .HasName("idx_diary_id"); 

                entity.HasIndex(e => e.Deleted) 
                    .HasName("idx_diary_deleted"); 

                entity.Property(e => e.Comments).HasColumnType("text"); 

                entity.Property(e => e.DateOf).HasColumnType("datetime"); 

                entity.Property(e => e.UpdateDate) 
                    .HasColumnType("datetime") 
                    .HasDefaultValueSql("(getdate())"); 
            }); 
```

## Step 5. Create IRepository file ->  Data -> IRepositories 

```
public interface IDiaryRepository: IRepository<Diary> 
{ 

} 
``` 

## Step 6. Create Repository file -> Data -> Repositories 
```
public DiaryRepository(MyDBContext dbContext) : base(dbContext) 
{ 
} 
```

## Step 7. Add Mapping -> Service -> Dxos -> Interface 

```
DiaryDto MapDiaryDto(Diary diary); 

List<DiaryDto> MapDiaryDto(List<Diary> diary); 
```

## Step 8. Add Mapping -> Service -> Dxos -> Implementation 
```
cfg.CreateMap<Diary, DiaryDto>() 

                .ForMember(dst => dst.diaryId, opt => opt.MapFrom(src => src.diaryId)) 

                .ForMember(dst => dst.Id, opt => opt.MapFrom(src => src.Id)) 

                .ForMember(dst => dst.Comments, opt => opt.MapFrom(src => src.Comments)) 

                .ForMember(dst => dst.AccountId, opt => opt.MapFrom(src => src.AccountId)) 

                .ForMember(dst => dst.DateOf, opt => opt.MapFrom(src => src.DateOf)) 

                .ForMember(dst => dst.Deleted, opt => opt.MapFrom(src => src.Deleted)) 

                .ForMember(dst => dst.DiaryNoteTypeid, opt => opt.MapFrom(src => src.DiaryNoteTypeid)) 

                .ForMember(dst => dst.UpdateDate, opt => opt.MapFrom(src => src.UpdateDate)) 

                .ForMember(dst => dst.UpdateUserId, opt => opt.MapFrom(src => src.UpdateUserId)) 

                .ForMember(dst => dst.diaryId, opt => opt.MapFrom(src => src.diaryId)); 
```
```
public DiaryDto MapDiaryDto(Diary diary) 
{ 
     return _mapper.Map<Diary, DiaryDto>(diary); 
} 
```
```
public List<DiaryDto> MapDiaryDto(List<Domain.Models.Diary> diary) 
{ 
   return _mapper.Map<List<Domain.Models.Diary>, List<Domain.Dtos.DiaryDto>>(diary); 
} 
```

## Step 9.  Add Service to Startup.cs 
```
services.AddScoped<IDiaryRepository, DiaryRepository>(); 
```
