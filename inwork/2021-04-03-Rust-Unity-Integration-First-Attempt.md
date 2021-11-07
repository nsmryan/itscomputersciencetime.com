+++
title = "First Attempt at Rust/Unity Integration"
[taxonomies]
categories = ["Language", "Programming", "Rust", "dll", "Unity", "c#", "ffi"]
+++



```c#
using SimpleJSON;
```

```c#
public enum Surface : byte {
    Floor,
    Rubble,
    Grass,
}
```

```rust
#[derive(Clone, Copy, Debug, PartialEq, Serialize, Deserialize)]
pub enum Surface {
    Floor,
    Rubble,
    Grass,
}
```

```c#
[StructLayout(LayoutKind.Sequential, Pack=0)]
public struct Tile {
    public bool blocked;
    public bool block_sight;
    public bool explored;
    public TileType tile_type;
    public Wall bottom_wall;
    public Wall left_wall;
    public byte chr;
    public Surface surface;
}
```

```rust
#[derive(Clone, Copy, Debug, PartialEq, Serialize, Deserialize)]
#[repr(C, packed)]
pub struct Tile {
    pub block_move: bool,
    pub block_sight: bool,
    pub explored: bool,
    pub tile_type: TileType,
    pub bottom_wall: Wall,
    pub left_wall: Wall,
    pub chr: u8,
    pub surface: Surface,
}
```

```c#
public struct GameRepr {
    public const int MSG_BUF_LEN = 1024;


    public IntPtr game;
    public Map map;
    public Dictionary<ulong, GameObject> entities;
    public IntPtr msg_buf;

    [DllImport("rust_roguelike", CallingConvention=CallingConvention.Cdecl)]
    public unsafe static extern IntPtr create_game(ulong seed, byte[] config_name, byte[] map_name);

    [DllImport("rust_roguelike", CallingConvention=CallingConvention.Cdecl)]
    public unsafe static extern void destroy_game(IntPtr game);

    [DllImport("rust_roguelike", CallingConvention=CallingConvention.Cdecl)]
    public unsafe static extern void step_game(IntPtr game, byte[] input_action);

    [DllImport("rust_roguelike", CallingConvention=CallingConvention.Cdecl)]
    public unsafe static extern void read_message(IntPtr game, IntPtr msg_ptr, IntPtr msg_len);

    [DllImport("rust_roguelike", CallingConvention=CallingConvention.Cdecl)]
    public unsafe static extern IntPtr read_map(IntPtr game, IntPtr width, IntPtr height);

    [DllImport("rust_roguelike", CallingConvention=CallingConvention.Cdecl)]
    public unsafe static extern IntPtr alloc_buffer(int buf_len);

    [DllImport("rust_roguelike", CallingConvention=CallingConvention.Cdecl)]
    public unsafe static extern void free_buffer(IntPtr ptr, int buf_len);

    public static byte[] ToRustString(String str) {
        byte[] bytes = new byte[str.Length + 1];
        System.Text.Encoding.ASCII.GetBytes(str, 0, str.Length, bytes, 0);
        return bytes;
    }
```

```c#
int tileSize = System.Runtime.InteropServices.Marshal.SizeOf(typeof(Tile));
```

```c#
        JSONNode msg = null;

        if (msg_len > 0) {
            string msg_string = Marshal.PtrToStringAnsi(msg_buf);
            //string msg_string = System.Text.Encoding.ASCII.GetString(msg_bytes, 0, msg_bytes.Length);
            msg = JSON.Parse(msg_string);
        }
```

```rust
#[no_mangle]
pub extern "C" fn create_game(seed: u64, config_name: *mut i8, map_name: *mut i8) -> *mut Game {
```

```rust
#[no_mangle]
pub extern "C" fn read_message(game_ptr: *mut Game, msg_ptr: *mut u8, msg_len: *mut i32) {
```

```rust
    let mut tile_buf = std::ptr::null_mut();
    unsafe {
        game = Box::from_raw(game_ptr);
```
        
        
```rust
    mem::forget(game);
```

