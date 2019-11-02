# Data Structure Design

## Format

This essentially boils down to GeoJSON vs. TopoJSON. 

If you care about size or topology, TopoJSON is the way to go. If you don't care about either, then use GeoJSON for simplicity's sake.

TopoJSON's primary advantage is size. By eliminating redundancy and using a more efficient fixed-precision integer encoding of coordinates, TopoJSON files are often an order of magnitude smaller than GeoJSON files.

The secondary advantage of TopoJSON is that encoding the topology has useful applications, such as topology-preserving simlification and automatic mesh generation.

These advantages come at a cost: a more complex file format. In JavaScript, you'd typically use the TopoJSON client library to convert TopoJSON to GeoJSON for use with standard tools such as `d3.geoPath`. Also, TopoJSON's integer format requires quantizing coordinates, which means that it can introduce rounding errors if you're not careful.

For server-side manipulation of geometries that does not require topology, then GeoJSON is probably the simpler choice. Otherwise, if you need topology or want to send the geometry over the wire to a client, then use TopoJSON.

Mike Bostock (creator of the d3.js library), [StackOverflow](https://stackoverflow.com/a/14765321)

It would be ideal to use TopoJSON given data would be distributed across the network to consuming clients.

The biggest concern is whether or not TopoJSON is compatible with the SQL Server `Geography` or `Geometry` column types. Because the `Json` column type [isn't fully supported](https://github.com/aspnet/EntityFrameworkCore/issues/4021) in Entity Framework Core yet, it isn't ideal to store TopoJSON in this format as far as I am aware (more research and testing is needed to validate this assumption). 

GeoJSON is compatible with the `Geometry`, but [with limitations](https://docs.microsoft.com/en-us/ef/core/modeling/spatial).

Given the data will be serialized and sent to the client anyway, it could be possible to just store the data as a string and serialize it to TopoJSON after receiving the HTTP call from within Angular.

## References

* Difference Between GeoJSON and TopoJSON, [StackOverflow](https://stackoverflow.com/a/14765321)
* Map JSON Values Stored in Database to EF Properties, [Entity Framework Core - GitHub](https://github.com/aspnet/EntityFrameworkCore/issues/4021)
* Spatial Data, [Entity Framework Core Docs](https://docs.microsoft.com/en-us/ef/core/modeling/spatial)