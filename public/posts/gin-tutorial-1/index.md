# Gin-Tutorial-1

In this chapter, we majorly deal with `project structure`, `interface`, `router`.
<!--more-->
## 1. Structure

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

## 2. Model

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
## 3. Router
### 3.1 router management
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
### 3.2 Router handler
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
### 3.3 Boot access
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

