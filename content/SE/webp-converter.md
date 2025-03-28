---
title: WebP Converter
draft: false
tags:
  - python
  - project
---

![banner](https://images.unsplash.com/photo-1523461811963-7f1023caeddd?w=500&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8Mnx8Y29sbGVjdGlvbiUyMG9mJTIwaW1hZ2V8ZW58MHx8MHx8fDA%3D)

I got tired of manually converting images for my [[digital garden]] to `.webp`. Most of my notes use PNG or JPEG images, which isn’t ideal since I host everything on GitHub, where free storage is limited. To save space, I decided to convert all my images to `.webp`—a far better choice for websites due to its smaller file sizes, faster loading times, and better performance [^4] .

I could’ve used existing tools, but I wanted more control. I needed a tool that let me pick which folders to process, skip specific directories, and optionally delete the originals—all while running from a simple CLI.

So, I built my own. Python’s `Pillow` [^1] library handles image conversions, and `os.walk()` [^2] makes it easy to traverse directories. The script follows a simple plan:

1. Scan a directory for images while allowing exclusions.
2. Convert them to `.webp`.
3. Optionally delete the originals.
4. Log everything to the terminal or a file.

Using Python’s `argparse` [^3], I made it flexible enough to tweak settings on the fly. The result? A lightweight CLI tool that does exactly what I need.

---

## Key Features

### 1. Recursively Scanning Directories

Since I wanted the script to work on entire folders (even deeply nested ones), I used `os.walk()` to traverse directories.

```python
for root, _, files in os.walk(folder):
    root_path = Path(root)
```

It walks through every subdirectory, listing files as it goes. This way, no matter how messy my image folders are, the script finds them all. I just need to know, what kind of image format that I need to switch to webp.

### 2. Ignoring and Allowing Specific Folders

I want to have the ability to skip  or ignore certain folder for conversion, the logic below will do just that, it will also cut of any uncessary time doing the conversion of unwanted folder, this method will handle if `--ignore` option is not empty:

```python
def should_skip_folder(folder_path, ignore_folders):
    return any(ignore.lower() in map(str.lower, folder_path.parts) for ignore in ignore_folders)
```

Likewise, `--allow` ensures that only specific folders get processed. If `allow_folders` is empty, it processes everything.

```python
def should_process_folder(folder_path, allow_folders):
    return not allow_folders or any(allow.lower() in map(str.lower, folder_path.parts) for allow in allow_folders)
```

### 3. Converting to WebP

The actual conversion is handled by Pillow. I chose to convert images to RGB mode before saving to WebP to avoid compatibility issues. Pillow also able to provide you ways to set the image quality, how you want your image to be compress:

```python
with Image.open(file_path) as img:
    img = img.convert("RGB")
    img.save(new_image_path, "WEBP", quality=settings.quality, optimize=True, method=settings.method)
```

I also added a `--resize` option for cases where I need to shrink images before conversion.

```python
if settings.resize:
    img.thumbnail(settings.resize, Image.LANCZOS)
```

### 4. Dry-Run Mode

Sometimes, I want to see what the script _would_ do before actually running it. That’s where `--dry-run` comes in. Instead of converting files, it just logs what would’ve happened.

```python
if settings.dry_run:
    logging.info(f"[DRY-RUN] Would convert: {relative_file} → {relative_new_image}")
```

### 5. Logging Everything

Without logs[^5] , debugging is just a guessing game. This method logs everything to both the console (for live tracking) and a file (for later review). Console shows `INFO` by default, while the file records everything, including `DEBUG` logs. If `debug=True`, the console shows all logs too.

Every session starts with a separator for clarity, and old handlers are cleared to prevent duplicate logs.

```python
def setup_logging(log_file, debug=False):
    """Configure logging to console and file."""
    log_format = "%(asctime)s - %(levelname)s - %(message)s"
    
    log_file = Path(log_file)
    log_file.parent.mkdir(parents=True, exist_ok=True)  

    logger = logging.getLogger()
    logger.setLevel(logging.DEBUG if debug else logging.INFO)
    logger.handlers.clear()

    console_handler = logging.StreamHandler()
    console_handler.setLevel(logging.DEBUG if debug else logging.INFO)
    console_handler.setFormatter(logging.Formatter(log_format))

    file_handler = logging.FileHandler(log_file, mode="a", encoding="utf-8")
    file_handler.setLevel(logging.DEBUG)
    file_handler.setFormatter(logging.Formatter(log_format))

    logger.addHandler(console_handler)
    logger.addHandler(file_handler)

    with open(log_file, "a", encoding="utf-8") as log:
        log.write(f"\n========== NEW SESSION {datetime.now():%Y-%m-%d %H:%M:%S} ==========\n")
```

Now, if something breaks, I don’t have to guess, I just check the logs.

### 6. Get Flexible with `argparse`

Hardcoding options is a pain. What if I want to process only a specific folder today but everything tomorrow? `argparse` makes the script adaptable. I can pass arguments on the fly, then choose directories I want, enable debugging, or delete originals if needed. Without touching the code each execution.

```python
def main():
    parser = argparse.ArgumentParser(description="Convert images to WebP format recursively.")
    parser.add_argument("folder", type=Path, help="Root folder to scan for images")
    parser.add_argument("--ignore", nargs="*", default=[], help="Folders to ignore (space-separated)")
    parser.add_argument("--allow", nargs="*", default=[], help="Folders to allow (if empty, process all)")
    parser.add_argument("--quality", type=int, default=75, help="Set WebP quality (1-100, lower = smaller file)")
    parser.add_argument("--method", type=int, choices=range(0, 7), default=6, help="Compression method (0=fastest, 6=best)")
    parser.add_argument("--resize", type=int, nargs=2, metavar=("WIDTH", "HEIGHT"), help="Resize images before conversion")
    parser.add_argument("--delete", action="store_true", help="Delete original images after conversion")
    parser.add_argument("--dry-run", action="store_true", help="Simulate the process without making changes")
    parser.add_argument("--debug", action="store_true", help="Enable debug logging")
```

Now, instead of modifying the script, for each usecase, I can just run:

```python
python main.py ~/Pictures --ignore public --allow content --quality 50 --method 6 --resize 1920 1080 --delete --dry-run
```

---

## Challenges & What I learned

### 1. Logging Issues

Initially, I used both `.debug` and `.info`, but enabling debug mode (`--debug`) flooded the terminal with too much detail. To fix this, I ensured debug logs only go into the log file while keeping the terminal clean with higher-level messages:

```python
logger = logging.getLogger()
logger.setLevel(logging.DEBUG if debug else logging.INFO)
logger.handlers.clear()

console_handler = logging.StreamHandler()
console_handler.setLevel(logging.DEBUG if debug else logging.INFO)
console_handler.setFormatter(logging.Formatter(log_format))
```

### 2. Preventing WebP Re-Deletion

I ran into an issue where converted I tried re-converted the `.webp` images. The images were getting deleted if `--delete` was enabled, because the naming and the extension of the file is similar  to when before the conversion. To fix this, I added:

```python
if settings.delete_originals and file_path.suffix.lower() not in (".webp"):
    ...
```

Now, WebP images stay intact after being re-converted.

### 3. Tracking Converted and Skipped Files

To improve visibility, I added counters for converted and skipped images in both the terminal and log file. This makes it easier to see what the script is doing:

```python
converted +=1 
skipped +=1
```

### 4. Struggles with PyTest

I attempted to add unit tests with PyTest[^6] . It was tricky at first, especially mocking file operations and simulating different directory structures. Writing tests for logging behavior was another challenge since log outputs aren’t as straightforward to validate.

### 5. Packaging with `setuptools` and `build`

I learned how to turn the script into an installable package using `setuptools`[^7] , then used the `build` [^8] module to generate a `.whl` file, making it easier to distribute and install. This turned the project from just a script into a proper dependency package.

### 6. Automating with GitHub Actions [^9]

I set up a `.yml` workflow to automate the conversion from script → dependency package → executable. Initially, I ran into [[Troubleshooting GitHub Actions Workflow|issues with permissions and dependencies in the workflow]], but after some debugging, I got it running smoothly. Now, every push triggers an automated build and package deployment.

## Final Thought

After a bit of trial and error, I now have a simple yet flexible tool that automates image conversion, saves storage, and speeds up load times. What started as a small convenience script turned into a useful project, teaching me a lot about logging, testing, packaging, and automation along the way.

If you’re interested, check it out here: [GitHub - webp-converter](https://github.com/farithadnan/webp-converter) 🚀

---

## 🔗 Sources

[^1]: [pillow · PyPI](https://pypi.org/project/pillow/)
[^2]: [Python os.walk()](https://www.w3schools.com/python/ref_os_walk.asp)
[^3]: [argparse — Parser for command-line options, arguments and subcommands — Python 3.13.2 documentation](https://docs.python.org/3/library/argparse.html)
[^4]: [Fetching Title#nda8](https://99designs.com/blog/tips/webp-image-format/)
[^5]: [logging — Logging facility for Python — Python 3.13.2 documentation](https://docs.python.org/3/library/logging.html)
[^6]: [pytest documentation](https://docs.pytest.org/en/stable/)
[^7]: [Building and Distributing Packages with Setuptools - setuptools 78.1.0.post20250325 documentation](https://setuptools.pypa.io/en/latest/setuptools.html)
[^8]: [build · PyPI](https://pypi.org/project/build/)
[^9]: [Building and testing Python - GitHub Docs](https://docs.github.com/en/actions/use-cases-and-examples/building-and-testing/building-and-testing-python)
