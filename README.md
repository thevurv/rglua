# ⚠️ Deprecated

There is a new, nicer to use, slimmer rglua on the horizon.

For the lua api equivalent to rglua, [autorun-lua](https://github.com/thevurv/Autorun-ng/tree/master/packages/autorun-lua)  
For access to source sdk bindings for gmod, [autorun-interfaces](https://github.com/thevurv/Autorun-ng/tree/master/packages/autorun-interfaces)

Don't let their names scare you. They're a part of the new Autorun-ng project, but similar to how rglua was to Autorun-rs, they will be able to be used outside of Autorun-ng.

Here's an example.

```rs
/// A basic example of creating a binary module using `autorun-lua`
/// Add this to your deps
/// { git = "https://github.com/thevurv/Autorun-ng", package = "autorun-lua" }
use autorun_lua::*;

// Or return anyhow::Result<f64>
fn lua_adder(lua: &LuaApi, state: *mut LuaState) -> Result<f64, Box<dyn std::error::Error>> {
	let x = lua.check_number(state, 1);
	let y = lua.check_number(state, 2);

	// This pushes it onto lua's stack for you.
	// You can return multiple values via a tuple of values
	// Additionally, Option<T> values work too, where None pushes nil.
	Ok(x + y)
}

#[unsafe(no_mangle)]
pub extern "C-unwind" fn gmod13_open(state: *mut LuaState) -> std::ffi::c_int {
	let lua = autorun_lua::get_api().expect("Failed to get lua api");

	lua.push_globals(state); // Push _G

	lua.push(state, "adder");
	lua.push(state, as_lua_function!(lua_adder));
	lua.set_table(state, -3); // _G["adder"] = lua_adder

	0
}
```

# 🌑 ``rglua`` [![cratesio](https://img.shields.io/crates/v/rglua.svg)](https://crates.io/crates/rglua) ![Build Status](https://github.com/Vurv78/rglua/actions/workflows/ci.yml/badge.svg) [![License](https://img.shields.io/github/license/Vurv78/rglua?color=red)](https://opensource.org/licenses/Apache-2.0) [![github/Vurv78](https://img.shields.io/discord/824727565948157963?label=Discord&logo=discord&logoColor=ffffff&labelColor=7289DA&color=2c2f33)](https://discord.gg/epJFC6cNsw)

This is a crate that allows interop with the (g)luajit c api as well as the source sdk through libloading and vtable bindings.
You can then use these for binary modules or manually injected code, like with [Autorun-rs](https://github.com/Vurv78/Autorun-rs)

More information on binary modules can be found on the garrysmod wiki: [Creating Binary Modules](https://wiki.facepunch.com/gmod/Creating_Binary_Modules) and examples [can be found here.](https://github.com/Vurv78/rglua/tree/master/examples)
## Usage
If you are targeting 32 bit make sure to install the toolchain and build to it:
```bash
rustup target add i686-pc-windows-msvc
cargo build --target=i686-pc-windows-msvc
```

## Comparison
There are actually a decent amount of libraries out there for gmod development.
Here's a comparison and why you could use this one.

[rglua]: https://crates.io/crates/rglua
[rust-glua-sys]: https://github.com/SpiralP/rust-glua-sys
[gmod-rs]: https://crates.io/crates/gmod
[gmrs]: https://github.com/diogo464/gmrs

| Library                           | [rglua] | [rust-glua-sys] | [gmod-rs]   | [gmrs] |
|-----------------------------------|---------|-----------------|-------------|--------|
| *Full* Lua C Api Bindings         | ✔️     | ❌              | ❌         | ❌    |
| On Crates.io                      | ✔️     | ❌              | ✔️         | ❌    |
| Proc Macros                       | ✔️     | ❌              | ✔️         | ✔️    |
| Interfacing w/  Source SDK        | ✔️     | ❌              | ❌         | ❌    |
| Returning Result<> from functions | ✔️	 | ❌              | ❌         | ✔️    |
| Can be used on stable             | ✔️     | ✔️              | ❌         | ✔️    |
| Real world examples               | ✔️     | ❌              | 〰️         | ✔️    |
| Linux / OSX Support               | ✔️     | ❌              | ✔️         | ✔️    |
| Github Stars                      | 😢     | 👍              | 👑         | 🤷‍♂️    |

__*You can help with that last one 😉*__

## Acknowledgements
### [garrysmod_common](https://github.com/danielga/garrysmod_common)
This is heavily based off of garrysmod_common, in how we export the lua_shared functions and trying to replicate everything from the Lua C Api.
