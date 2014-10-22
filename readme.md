# A (mostly) type-safe GLFW C++ wrapper with event-polling

Requirements: C++11 compatible compiler, GLFW3, Boost.Lockfree.Queue.
License: [Boost Software License 1.0](http://www.boost.org/users/license.html);

## Configuration

- Error handling:
  - by default throws on errors, but
  - custom error handling is supported: define `GLFW_CXX_CUSTOM_ERROR_HANDLING`
    - this requires you to define the functions
      `report_logic_error(std::string)` and `report_runtime_error(std::string)`
      in the `glfw` namespace _before_ including the library wrapper.
- Max number of events per window: define `GLFW_CXX_MAX_EVENTS` _before_
  including the library.

## Example:

```c++
#include <glfw/glfw.hpp>

glfw::environment::static_data glfw::environment::data;

int main() {
  glfw::environment env;

  glfw::window::hint(glfw::window_attribute::opengl_profile,
                     GLFW_OPENGL_CORE_PROFILE);
  glfw::window::hint(glfw::window_attribute::opengl_forward_compat, GL_TRUE);
  glfw::window::hint(glfw::window_attribute::context_version_major, 4);
  glfw::window::hint(glfw::window_attribute::context_version_minor, 1);

  glfw::window window{{1024, 768}, "my window"};

  while (window) {
    glfw::event event;
    while (window.poll(event)) {
      fprintf(stdout, "event: %s", to_string(event.type).c_str());
      switch (event.type) {
        case glfw::event_type::window_closed: {
          window.close();
          break;
        }
        case glfw::event_type::key_pressed: {
          if (event.key.key == glfw::key::ESCAPE) {
            window.close();
            break;
          }
        }
      }
    }
  }
  return 0;
}
```

## Todo:

- enums for the configuration
- clean up the static storage of environment a bit.

## Main types:

- `glfw::environment`: only one instance should be created (typically by the
  main thread at the beginning of `main`.

- `glfw::window`: owning window (movable, non-copyable, semi-regular).
- `glfw::event` (semi-regular): `event_type` + a union of event data.
- `glfw::modifier_keys`:
- `glfw::monitor` (semi-regular):
- `glfw::video_mode` (regular):
- `glfw::gamma_ramp` (regular):

## Type-safe enums:

- `glfw::event_type`:
- `glfw::key`:
- `glfw::mouse_button`:
- `glfw::window_attribute`:

## Innards:

When a window is created an event queue is added to the `glfw::environment` for
event polling. This is what allows you to poll events directly from the window.
