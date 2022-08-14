
Tetralux
fn NormalSlice(comptime T: type) type {
    var ti = @typeInfo(T);
    if (ti != .Pointer) @compileError("wanted slice or pointer to an array; given '" ++ @typeName(T) ++ "'");
    ti.Pointer.size = .Slice;
    ti.Pointer.is_const = true;
    return @Type(ti);
}
5-142857
pablo
