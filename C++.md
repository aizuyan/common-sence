### std::shared_ptr 封装
有些类库，不支持标准的 std::shared_ptr，对其进行封装后使用，比如lua
```cpp
template <class T, void( *deleter ) (T *)> class SmartPtr : public std::shared_ptr<T> {
public:
	SmartPtr( T* object ) : std::shared_ptr<T>( object, deleter )
	{
	}
};

void luaStateDeleter( lua_State* s )
{
	std::cout << "CLOSE" << std::endl;
	lua_close( s );
}


using LuaPtr = SmartPtr<lua_State, luaStateDeleter>;

LuaPtr y( luaL_newstate() );
```
