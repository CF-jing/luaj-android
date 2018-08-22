# luaj安卓框架
本项目由[luaj](https://github.com/luaj/luaj)的`jse`版本为基础修改而来。语法上与`luaj`并无不一样，主要修改了几处安卓平台上的引用错误。

## 使用
### 引入库

```
compile 'com.reizx:luaj-android:3.0.1'
```
### 使用例子
以下例子都会输出以`luaj-tag`为tag的日志。
#### 执行文件（[demo](app\src\main\java\com\reizx\luaj\view\fragment\HomeFragment.java)）
```
    public void invokeFile() {
        String path = "/sdcard/SimpleExample.lua";
        // copy the script to path
        ResourceUtils.copyFileFromAssets("SimpleExample.lua", path);
        // init global before
        // create an environment to run in
        // Globals globals = JsePlatform.standardGlobals();
        // Use the convenience function on Globals to load a chunk.
        LuaValue chunk = globals.loadfile(path);
        // Use any of the "call()" or "invoke()" functions directly on the chunk.
        chunk.invoke();
    }
```

#### 执行脚本流
```
    public void invokeStream() {
        String path = "/sdcard/SimpleExample.lua";
        // get the script InputStream
        InputStream in = new ByteArrayInputStream(ResourceUtils.readAssets2String("SimpleExample.lua").getBytes());
        // init global before
        // create an environment to run in
        // Globals globals = JsePlatform.standardGlobals();
        // Use the convenience function on Globals to load a chunk.
        /** Load the content form an input stream as a binary chunk or text file.
         * @param is InputStream containing a lua script or compiled lua"
         * @param chunkname Name that will be used within the chunk as the source.
         * @param mode String containing 'b' or 't' or both to control loading as binary or text or either.
         * @param environment LuaTable to be used as the environment for the loaded function.
         * */
        LuaValue chunk = globals.load(in, "@"+"Simple", "bt", globals);
        // Use any of the "call()" or "invoke()" functions directly on the chunk.
        chunk.invoke();
    }
```

#### 自定义接口
```
    public void invokeCustom(){
        Globals globals = customEvn();

        // get the script InputStream
        InputStream in = new ByteArrayInputStream(ResourceUtils.readAssets2String("hyperbolicapp.lua").getBytes());
        LuaValue chunk = globals.load(in, "@"+"hyperbolicapp", "bt", globals);
        // Use any of the "call()" or "invoke()" functions directly on the chunk.
        chunk.invoke();
    }
    
    /**
     * 自定义lua环境，可以自定义API
     * @return
     */
    public Globals customEvn(){
        Globals globals = new Globals();
        globals.load(new JseBaseLib());
        globals.load(new PackageLib());
        globals.load(new Bit32Lib());
        globals.load(new TableLib());
        globals.load(new StringLib());
        globals.load(new CoroutineLib());
        globals.load(new JseMathLib());
        globals.load(new JseIoLib());
        globals.load(new JseOsLib());
        globals.load(new LuajavaLib());
        //todo register library
        globals.load(new hyperbolic());

        LoadState.install(globals);
        LuaC.install(globals);
        return globals;
    }
```

### 修改日志
* 修复`classloader`引用错误。