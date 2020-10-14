Particulate is a Unity plugin to render Potree formatted pointclouds. It can stream pointclouds of any size or level of detail to any device, without impacting framerates or quality.

*This article was last updated on 2020-10-05. Feel free to reach out if you're interested in hearing more, my contact details are on the homepage.*

## Motivation
Particulate was a fun project to build, both because it was technically involved and because it directly addressed specific business problems that both Unsigned Studio and Aurecon faced. On the Unsigned side, we had projects with massive amounts of data, and no feasible way to deliver them on low-end client devices, like cheap laptops and iPads. On the Aurecon side, we had clients and teams capturing so much asset data, including full site scans, but they were struggling to utilise this deluge of unstructured data.

From this lens, integrating a point cloud viewer into Unity and siteLab was an obvious move. Being able to stream model data at varying levels of detail was perfect for a "build once, deliver anywhere" dream, internal teams suddenly found that their data was more valuable, and overall it would increase our ability to get siteLab into client's hands, which has always been one of the biggest drivers of siteLab's growth. Clients are much more inventive than we could ever be, and they understand their own problems more than we do!

We had previously delivered one point cloud to a client through [Potree](https://github.com/potree/potree), a web based point cloud streaming app and associated data format. Potree's data format is very well described and their viewer is incredibly fast, even in Javascript in the browser on low-end devices. As such, we decided to use Potree as the basis of our point cloud viewer, ingesting clouds in the same format and rendering using similar mechanisms.

## Implementation

_I'm not sure how much I can talk about this publically, so hold tight. If you're interested, please get in contact with me, details in the header._

## Selection
Since one of the major drivers of deliviering point clouds is to reduce the geometry on device, using tagged individual collision geometry for object selection (eg. clicking on a fire hydrant to get an ID that can be linked to BIM data) is a hard choice to stomach. We've developed an alternative approach with much lower runtime cost. Instead of having an individual collider and BIM data component for each object, using a 3DS Max plugin we generate one single optimised mesh for the entire geometry, with object IDs encoded in vertex colours and a map from ID to BIM data exported separately. With this, whenever we want to inspect BIM data, we can render _just the optimised mesh_ (aligned with the pointcloud), capture the screen region around the cursor, and extract the ID from the vertex colour, which we can use to look up the BIM data. 

Most of the work is on the Max plugin side, and the Unity component is inspired by the method Potree uses for point selection. We're very excited to deliver this on the next point cloud project that we win.

## Future steps
In future, I'm interested in implementing eye-dome lighting, which is roughly the point cloud equivalent of edge detection or screen-space ambient occlusion (SSAO). Unfortunately, Unity doesn't write to the depth texture when using geometry textures, so there's a number of steps to go to generate the correct fallback/shadow caster path so we can access that depth texture. 

The other part I'm looking forward to working on is generating satisfying collision geometry using the point cloud as a base. This is useful for navigating the cloud in VR -- while selection on points can be done in screen-space for clicking, it won't work nearly as well for a raycasted teleporter arc. Meshes can be generated offline, and meshing point cloud geometry is a well known problem, but there are downsides: the geometry is fairly heavy and dirty, the time requirement is high, and it's offline. A real time/load time solution would be beneficial for loading arbitrary clouds.
