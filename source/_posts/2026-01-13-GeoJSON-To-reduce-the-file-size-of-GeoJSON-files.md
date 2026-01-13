---
layout: post
title: GeoJSON | To reduce the file size of GeoJSON files
categories: [GeoJSON]
tags: [GeoJSON, NWP]
author: wpsze
date: 2026-01-13 06:08:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/9efzRbQ.png
banner_img: https://i.imgur.com/9efzRbQ.png
---

To reduce the file size of your `data.geojson` by skipping points (decimation), you can use the following **Python** script. This script iterates through the geometries (LineStrings, Polygons, etc.) and samples every $n$-th point while ensuring that polygons remain closed.

-_`$ python reduce_geojson.py data.geojson`
- `$ python reduce_geojson.py data.geojson --step 5`
- `$ python reduce_geojson.py data.geojson -s 4 -o small_map.geojson`


```python
import json
import argparse
import os

def reduce_geometry(geometry, step):
    """
    Reduces the number of points in a geometry by taking every n-th point (step).
    """
    if geometry is None:
        return None
    
    geom_type = geometry.get('type')
    coords = geometry.get('coordinates')
    
    if geom_type == 'Point':
        return geometry
    
    if geom_type == 'MultiPoint':
        geometry['coordinates'] = coords[::step]
        return geometry
    
    if geom_type == 'LineString':
        new_coords = coords[::step]
        if len(new_coords) < 2 and len(coords) >= 2:
            new_coords = [coords[0], coords[-1]]
        geometry['coordinates'] = new_coords
        return geometry
    
    if geom_type == 'Polygon':
        new_coords = []
        for ring in coords:
            sampled = ring[::step]
            if sampled[-1] != ring[-1]:
                sampled.append(ring[-1])
            if len(sampled) < 4 and len(ring) >= 4:
                sampled = [ring[0], ring[len(ring)//3], ring[2*len(ring)//3], ring[-1]]
            new_coords.append(sampled)
        geometry['coordinates'] = new_coords
        return geometry

    if geom_type in ['MultiLineString', 'MultiPolygon']:
        # This handles nested coordinate structures
        def process_recursive(c, depth):
            if depth == 1: # Reached the list of points (LineString/Ring)
                sampled = c[::step]
                if geom_type == 'MultiPolygon' and sampled[-1] != c[-1]:
                    sampled.append(c[-1])
                return sampled
            return [process_recursive(sub, depth - 1) for sub in c]
        
        depth = 2 if geom_type == 'MultiLineString' else 3
        geometry['coordinates'] = process_recursive(coords, depth)
        return geometry

    return geometry

def main():
    parser = argparse.ArgumentParser(description="Reduce GeoJSON file size by skipping coordinate points.")
    
    # Positional argument for the file
    parser.add_argument("input", help="Path to the input data.geojson file")
    
    # Optional arguments
    parser.add_argument("-s", "--step", type=int, default=3, 
                        help="The step size (e.g., 3 to keep every 3rd point). Default is 3.")
    parser.add_argument("-o", "--output", help="Output filename. Defaults to 'reduced_<input>'")

    args = parser.parse_args()

    # Determine output name if not provided
    if not args.output:
        base, ext = os.path.splitext(args.input)
        args.output = f"{base}_reduced{ext}"

    try:
        with open(args.input, 'r') as f:
            data = json.load(f)

        if data.get('type') == 'FeatureCollection':
            for feature in data.get('features', []):
                feature['geometry'] = reduce_geometry(feature.get('geometry'), args.step)
        elif data.get('type') == 'Feature':
            data['geometry'] = reduce_geometry(data.get('geometry'), args.step)
        else:
            data = reduce_geometry(data, args.step)

        with open(args.output, 'w') as f:
            json.dump(data, f)
        
        print(f"Success! Reduced file saved as: {args.output}")
        print(f"Sampling rate: Kept 1 every {args.step} points.")

    except FileNotFoundError:
        print(f"Error: The file '{args.input}' was not found.")
    except json.JSONDecodeError:
        print(f"Error: The file '{args.input}' is not a valid JSON/GeoJSON.")

if __name__ == "__main__":
    main()
```
