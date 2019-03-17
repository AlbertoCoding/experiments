# experiments
Experimental repository


# Python Code
'''python
@app.route("/information/&file=<path:filename>", methods=['GET'])
def deserialization(filename):
     print(filename, file=sys.stdout)
     if filename == 'file.yml':
         with open(filename, 'rb') as handle:
            if filename == 'file.yml':
                # Import the PyYAML dependency
                with open(filename) as yaml_file:
                # Unsafely deserialize the contents of the YAML file
                    content = yaml.load(yaml_file)
'''
