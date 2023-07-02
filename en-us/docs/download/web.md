# Getting Started with Siv3D for Web
An unofficial version of Siv3D that can generate programs that run in a web browser is available. There are several limitations and points to note for the web version, so it is intended for intermediate and advanced users who are familiar with using Siv3D. If you have any trouble using it, please ask questions in the `#web` channel on the Siv3D Discord server.

## Usage Guide
- Please refer to [OpenSiv3D for Web :material-open-in-new:](https://siv3d.kamenokosoft.com/docs/en/){:target="_blank"}.
- In the initial build, an error message may appear, but if you build it again, it will build successfully.
- In the web version build, all files of `engine/` and `example/` are included in the final output by default, making the final output file size several tens of MB even in the Release build. When you publish such an application on the web, it takes time for the users accessing it to download the files. Therefore, when you actually publish the application, you need to delete unnecessary files (see: [Tutorial 60. Publishing the Application](../../tutorial3/release/) for reference).
- By removing libraries that are not used by the program from the "additional dependency files" in the Emscripten linker settings (e.g. `Siv3DScript`, `opencv_objdetect`, `opencv_photo`, `opencv_imgproc`, `turbojpeg`, `gif`, `webp`, `opusfile`, `opus`, `tiff`), the output file size of the web version can be compacted to **a minimum of a few MBs**. For more details, please consult in the `#web` channel on the Siv3D Discord server.
