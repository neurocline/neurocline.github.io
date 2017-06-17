---
categories: dev
date: 2016-05-29T11:57:00Z
tags: links
title: Recent links
url: /2016/05/29/recent-links/
---

Good practice for patching/hooking DirectX

- if Present() returns D3DERR_DEVICELOST, then free every resource you are holding
- hook TestCooperativeLevel and Reset to be in sync with what the game is thinking (and again, if you get D3DERR_DEVICELOST, free everything and wait)
- what's important is freeing everything allocated into the D3DPOOL_DEFAULT memory class. But if you are holding resources, you'll prevent the app from closing DirectX (which it might want to do in order to do a stronger version of reset).

[Lost Devices (Direct3D 9)](https://msdn.microsoft.com/en-us/library/windows/desktop/bb174714(v=vs.85).aspx)

[IDirect3DDevice9::Present method](https://msdn.microsoft.com/en-us/library/windows/desktop/bb174423(v=vs.85).aspx)

[TsudaKageyu/minhook](https://github.com/TsudaKageyu/minhook)

[Create DirectX9Texture from RGBA (const char*) buffer](http://stackoverflow.com/questions/27970403/create-directx9texture-from-rgba-const-char-buffer)

[Direct3D 9 to Direct3D 10 Considerations (Direct3D 10)](https://msdn.microsoft.com/en-us/library/windows/desktop/bb205073(v=vs.85).aspx)

[Help with DirectX 9 Textures](http://www.cplusplus.com/forum/windows/53725/)

[Tutorial 11: 2D Rendering](http://www.rastertek.com/dx11tut11.html)

[Directly Mapping Texels to Pixels (Direct3D 9)](https://msdn.microsoft.com/en-us/library/windows/desktop/bb219690(v=vs.85).aspx)

[2D in Direct3D using Textured Quads](http://www.gamedev.net/page/resources/_/technical/directx-and-xna/2d-in-direct3d-using-textured-quads-r1972)

[Drawing a Texture to the Screen](http://stackoverflow.com/questions/16346184/drawing-a-texture-to-the-screen)

[IDirect3DDevice9::UpdateTexture method](https://msdn.microsoft.com/en-us/library/windows/desktop/bb205858(v=vs.85).aspx)

[IDirect3DTexture9::LockRect method](https://msdn.microsoft.com/en-us/library/windows/desktop/bb205913(v=vs.85).aspx)

[IDirect3DDevice9::UpdateSurface method](https://msdn.microsoft.com/en-us/library/windows/desktop/bb205857(v=vs.85).aspx)
