---
layout: post
title: Linux | Python Template
categories: [Linux]
tags: [python, template]
author: wpsze
date: 2025-07-23 15:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/b/bd/Python_Powered.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/b/bd/Python_Powered.png
---

Template for a Python script that includes logging and argparse. You can use this as a starting point for your projects:

```python
#!/bin/python
# -*- coding: utf-8 -*-

import argparse
import logging

def setup_logging(level=logging.INFO):
    """Set up logging configuration."""
    logging.basicConfig(
        level=level,
        format='%(asctime)s - %(levelname)s - %(message)s'
    )

def main(args):
    """Main function to execute the script."""
    logging.info("Starting the script...")
    
    # Example of using the command-line arguments
    if args.verbose:
        logging.setLevel(logging.DEBUG)
        logging.debug("Verbose mode is on.")

    logging.info(f"Argument value: {args.value}")
    
    # Add your script logic here
    # ...

    logging.info("Script finished successfully.")

if __name__ == "__main__":
    # Set up argument parsing
    parser = argparse.ArgumentParser(description="A template for a Python script.")
    parser.add_argument(
        '-v', '--value',
        type=str,
        required=True,
        help='An example argument value.'
    )
    parser.add_argument(
        '-vv', '--verbose',
        action='store_true',
        help='Enable verbose logging.'
    )
    
    args = parser.parse_args()

    # Set up logging
    setup_logging()

    # Run the main function
    main(args)
```