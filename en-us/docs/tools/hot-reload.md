# Hot Reload

## Hot Reload Features
Using Visual Studio's hot reload feature (the 🔥 button in Visual Studio's debug menu), you can apply numerical changes and code additions to a running program without restarting it. By default, it is only enabled when running a Debug build with a debugger attached.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tools/hot-reload.gif)

## Hot Reload Limitations
Adjustment tasks such as adding shapes, changing drawing positions, and color changes can often be hot reloaded, which can accelerate the adjustment cycle. However, there are code change operations that don't support hot reload, so it's difficult to complete a program using only hot reload. Future Visual Studio updates may expand the range of operations that can be hot reloaded.

Hot reload cannot rewind processing, so already loaded textures and audio will not be reloaded. To reflect changes to textures and audio, you need to restart the program.