---
title: "Gin-Tutorial-1"
date: 2021-03-10T03:06:56+08:00
tags: ["Go", "Backend"]
categories: ["Documentation"]

resources:
- name: "featured-image"
  src: "featured-image.jpg"


lightgallery: true

toc:
  auto: false

license: '<a rel="license external nofollow noopener noreffer" href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank">CC BY-NC 4.0</a>'
---
In this chapter, we majorly deal with `project structure`, `interface`, `router`.
<!--more-->
## structure

	blog-service
	├── configs
	├── docs
	├── global
	├── internal
	│   ├── dao
	│   ├── middleware
	│   ├── model
	│   ├── routers
	│   └── service
	├── pkg
	├── storage
	├── scripts
	└── third_party

## Model

The attributes of objects have in common should create an individual model to store


	.
	├── internal/
	│   └── model/
	│       ├──model.go
	│       ├──tag.go
	│       ├──article.go
	│       └──article_tag.go

and in each table model should contain the `*Model`
#### tag.go
```go
type Tag struct {
	*Model
	Name  string `json:"name"`
	State uint8  `json:"state"`
}

func (t Tag) TableName() string {
	return "blog_tag"
}
```
## Router
### Router management
decide the business api and declare the create method in `internal/routers` 
```go
router.go
func NewRouter() *gin.Engine {
	r := gin.New()
	r.Use(gin.Logger())
	r.Use(gin.Recovery())
	
	apiv1 := r.Group("/api/v1")
	{
		apiv1.POST("/tags")
		apiv1.DELETE("/tags/:id")
		apiv1.PUT("/tags/:id")
		apiv1.PATCH("/tags/:id/state")
		apiv1.GET("/tags")
		
		apiv1.POST("/articles")
		apiv1.DELETE("/articles/:id")
		apiv1.PUT("/articles/:id")
		apiv1.PATCH("/articles/:id/state")
		apiv1.GET("/articles/:id")
		apiv1.GET("/articles")
	}

	return r
}
```
### Router handler
api handler should be stored inside the `internal/routers/api`, dir name is the api's version
#### tag.go
```go
type Tag struct {}

func NewTag() Tag {
	return Tag{}
}

func (t Tag) Get(c *gin.Context) {}
func (t Tag) List(c *gin.Context) {}
func (t Tag) Create(c *gin.Context) {}
func (t Tag) Update(c *gin.Context) {}
func (t Tag) Delete(c *gin.Context) {}
```
### Boot access
modify `main.go`, set parameters like TCP Endpoint, Readtime of `http.Server`
#### main.go
```go
func main() {
	router := routers.NewRouter()
	s := &http.Server{
		Addr:           ":8080",
		Handler:        router,
		ReadTimeout:    10 * time.Second,
		WriteTimeout:   10 * time.Second,
		MaxHeaderBytes: 1 << 20,
	}
	s.ListenAndServe()
}
```
