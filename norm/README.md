## git协作规范
对于每个团队成员
1. 将本仓库fork到你的github账户下
2. 将你账户中fork过来的仓库clone到你的本地计算机中
3. 设置仓库的upstream
```bash
git remote add upstream https://github.com/Homebrew-Blockchain-Club/minichain.git
```
4. 新建分支，名为dev
```bash
git branch dev
git checkout dev
```
5. 上游有新更新时与上游主干同步
```bash
git checkout main
git pull upstream main
git checkout dev
git merge main
```
6. 完成一次版本更迭，先提交到自己的仓库，再发起pull request
## package规范
1. main.go直接在根目录下，而其依赖的每个package独占一个文件夹
4. package应当按照明确区分的功能命名，避免如controller、models这样的扁平命名，而是如user、auth、crypto等明确的功能区分。
5. 同一包内不同方面的功能应拆分到不同的文件内，假如存在一个package user，其功能主要是进行用户登录、注册等操作，应当将其对应实现拆分为login.go、register.go而非合为user.go
## 分组编码规范
1. 组内不同的人会负责不同的package，请不要更改他人的package，这样可以避免合并冲突 
2. 如果需要自己依赖而另一人尚未完成的函数，应当按照如下的方式实现一个伪函数，从而实现小组内工作的并行，且方便每个人各自的单元测试，样例如下：
```go
package my_package
import his_package
const HIS_PACKAGE_HISFUNC_UNIMPLEMENTED=true
func MyFunc(){
    var a T
    if HIS_PACKAGE_HISFUNC_UNIMPLEMENTED{
        a=pseudoHisFunc()
    }else{
        a=his_package.HisFunc()
    }
}
func pseudoHisFunc() T{
...
}
```
3. 对于结构体的情况，interface.go提供了Abstract接口，就以如下方式实现一个mock结构体，样例如下：
```go
package my_package
import his_package
const HIS_PACKAGE_HISFUNC_UNIMPLEMENTED=true
func MyFunc(){
    var a AbstractHisStruct
    if HIS_PACKAGE_HISFUNC_UNIMPLEMENTED{
        a=NewMockHisStruct()
    }else{
        a=NewHisStruct()
    }
    a.DoSomething()
}
```
当你的函数经过了充分的测试，证明其本身工作没有问题、且其他人的工作也已经完成时，将HIS_PACKAGE_HISFUNC_UNIMPLEMENTED设为false以实现对接
## 代码文件规范
1. 代码文件使用蛇形命名法，如source_code.go
2. 每个函数都应给出其相应的注释，其应符合pkgsite的约束，以方便明确自己实现的目标，以及和其他人的交互。pkgsite注释样例如下
```go
// Package pet provides structures and functions for pet management.
package pet

// Pet represents a pet with a name, age, and species.
type Pet struct {
  Name    string
  Age     int
  Species string
}
```
pkgsite注释文档符合Markdown格式，其应刚好位于被注释内容的上一行，效果如下：![效果](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6bfcb077-75b7-4fe9-ac2c-f4e49402e229_700x900.png)  
3. 当你需要本项目其对应的网页文档时，请先下载pkgsite，然后用pkgsite打开本项目，即可看到由注释自动生成的文档了。
```bash
go install pkgsite
pkgsite -open .
```
3. 函数应使用驼峰命名法，语言是英语，对于对外的接口，则需要首字母大写。
## 测试规范
1. testing文件夹应当放在对应的package中，格式如下：
```go
// In file my_package_test.go
package test
import (
    "testing"
    "your_package"
)
func TestMyFunc(t *testing.T){

}
```