```javascript
// Npm install 安装依赖报错一般如何处理?
// 可能是node npm 版本问题， 使用nvm工具 切换 node版本
// nvm install 10.24.1 
// nvm use 10.24.1 
// 如果出现 exit status 5, 1 说明权限不够，解决方法是
// 用管理员权限打开vscode

//gitee  --
//git config --list 
//git config user.name  git config user.email
//git add .  git status  git commit -m 'xxx'
// git remote -v 
// git remote add origin https://gitee.com/zjl429/fireblog.git
// git push -u origin "master"




```

```javascript
Object.defineProperty(store, '$state', {
  get: ()=> (__DEV__ && hot ? hotState.value : pinia.state.value[$id]),
  set: (state) => {
	if(__DEV__ && hot) {
	  throw new Error('can not set hotState')
	}
	$patch($state) => {
	  assgin($state, state)
	})
  },
})
```

