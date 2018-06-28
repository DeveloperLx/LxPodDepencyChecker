# LxPodDepencyChecker

# coding:utf-8
import sys, os, json
reload(sys)
sys.setdefaultencoding("utf-8")

PATH = '...'
SEARCH_KEY = '#import '

def union(list_a, list_b):
    return list(set(list_a).union(set(list_b)))

def traverse_file_path(rootdir):
    file_list = []
    for parent, dirnames, filenames in os.walk(rootdir):
        for filename in filenames:
            file_list.append(os.path.join(parent, filename))
    return file_list

def search_pics_in_single_code(single_code_path, search_key=[SEARCH_KEY]):
    try:
        with open(single_code_path, 'rb') as file:
            result = []
            for line in file.readlines():
                for keyword in search_key:
                    res = match(line, search_key=keyword)
                    result = union(result, res)
            return result
    except Exception, e:
        return []

def match(string, search_key=''):
    result = []
    while len(string) > 1 and string.find(search_key) != -1:
        result.append(string)
    return result

def get_whole_file_name(file_path):
	return file_path[file_path.rfind('/') + 1:]

def do(files):
    result_string = ''
    for each_path in files:
        result = search_pics_in_single_code(each_path, search_key=[SEARCH_KEY])
        if len(result) != 0:
            result_string += "// " + get_whole_file_name(each_path)+ "\n"
            for local_str in result:
                result_string += "%s" %(local_str) + "\n"
            result_string += ''+ "\n"
    return result_string


files = traverse_file_path(PATH)
result_string = do(files)
# print result_string;

# 将产生的结果写入文件
file(os.path.abspath(os.path.dirname(sys.argv[0])) +"/"+ "result.txt", 'w').writelines(result_string)
