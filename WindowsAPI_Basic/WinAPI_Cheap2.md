# 第二章

## 窗口过程

```

LRESULT WINAPI WindowProc(
    _In_ HWND hWnd,
    _In_ UINT uMsg,
    _In_ WPARAM wParam,
    _In_ LPARAM lParam);

```

`hwnd` : 表示接收消息的窗口  
`uMsg` : 表示消息的类型(WM_CREATE\WM_PAINT)  
`wParam` & `lParam` : 提供了更丰富的信息  

回调函数,用于处理windows发送的各类消息