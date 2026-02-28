# App_Utility
App utility header for setting up apps quickly. WINDOWS ONLY. Used with SLUGS(Graphics wrapper)

This header file made to make setting up a window for the Windows OS much easier. 

It was designed to be used with my Direct X wrapper library(SLUGS) as well as my Dear_Imgui wrapper library(Slimgui)

It works by taking in function pointers to functions that you want to run as a startup,frameloop, and cleanup function. In the mainline for you program
you would run an initializer function that accepts the function pointers, and then call the app_mainline function that will run eveything for you:
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
