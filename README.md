# App_Utility
App utility header for setting up apps quickly. WINDOWS ONLY. Used with SLUGS(Graphics wrapper)

This STB style header file made to make setting up a window for the Windows OS much easier.

**INCLUDING THIS FILE:**
It's a header only file: Just add it to the same directory as your C program and at the top of the main .c file
```C
#define APP_UTILITY_IMPLEMENTATION
#include "app_utility.h"
```

It was designed to be used with my Direct X wrapper library(SLUGS) as well as my Dear_Imgui wrapper library(Slimgui)
It works by taking in function pointers to functions that you want to run as a startup,frameloop, and cleanup function. In the mainline for you program
you would run an initializer function that accepts the function pointers, and then call the app_mainline function that will run eveything for you.
*init_func will run on startup.
*frame_func will run every frame.
*cleanup_func will run on program termination.
*event_func will run on OS events(inputs, etc).
Function signatures do matter for your functions you define in your main program

its expecting:
void init();
void cleanup();
void frame();
void event(app_event_t* event);
```C
int main(){
    app_init((app_desc_t){
        .frame_func = frame,
        .init_func = init,
        .cleanup_func = cleanup,
        .event_func = event,
        .width = 800,
        .height = 600
    });
    app_mainline();
}
```
The width and height parameters are the dimensions of your application window.
Example init func:
```C
void init(){
    uint8_t arena_backingBuffer[131072];
    arena_init(&slg_arena,arena_backingBuffer,131072);
    slg_arena.name = "main_arena";
    slg_d3d12_state.appdata.width = 800;
    slg_d3d12_state.appdata.height = 600;
    slg_d3d12_state.appdata.name = "test app";
    slg_d3d12_state.appdata.hwnd = app_get_window_handle();
    slg_setup();
    slimgui_setup();
    slg_close_setup();
}
```
Example frame func:
```C
void frame(){
    app_get_cursor_pos(&slimgui_input_state.mouse_x,&slimgui_input_state.mouse_y);
    slg_begin_frame();
    slg_begin_pass();
    slimgui_frame();

    ImVec2 pos = { 10.0f, 10.0f };
    igSetNextWindowPos(pos, ImGuiCond_Once);
    igBegin("Main", NULL, 0);
    igText("custom main window");
    igEnd();

    slimgui_end_frame();
    slg_submit_draw();
}
```
Example cleanup func:
```C
void cleanup(){
    slg_cleanup();
}
```
Example event func:
```C
void event(app_event_t* event){
    switch(event->event_code){
        case(APP_EVENT_MOUSE_LEFT_BUTTON_DOWN):{
            slimgui_input_state.left_mouse_down = true;
        }
        break;
        case(APP_EVENT_MOUSE_LEFT_BUTTON_UP):{
            slimgui_input_state.left_mouse_down = false;
        }
        break;
        case(APP_EVENT_MOUSE_RIGHT_BUTTON_DOWN):{
            slimgui_input_state.right_mouse_down = true;
        }
        break;
        case(APP_EVENT_MOUSE_RIGHT_BUTTON_UP):{
            slimgui_input_state.right_mouse_down = false;
        }
        break;
        case(APP_EVENT_SCROLL_WHEEL):{
            slimgui_input_state.mouse_scroll_delta = event->mouse_wheel_delta;
            event->mouse_wheel_delta = 0;
        }

    }
}
```

