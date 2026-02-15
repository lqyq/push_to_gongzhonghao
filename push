import requests
import json

# -------------------------- 配置项（替换成你自己的） --------------------------
APP_ID = "wx415cb37597179575"
APP_SECRET = "5a06c1786e2c150e82366b5a2d999307"
COVER_IMAGE_PATH = "1.jpg"  # 封面图本地路径（可选）


# -----------------------------------------------------------------------------

def get_access_token(app_id, app_secret):
    """获取微信公众号 Access Token"""
    url = f"https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={app_id}&secret={app_secret}"
    try:
        response = requests.get(url)
        result = response.json()
        if "access_token" in result:
            return result["access_token"]
        else:
            raise Exception(f"获取 Access Token 失败：{result}")
    except Exception as e:
        print(f"获取 Access Token 异常：{e}")
        return None


def upload_cover_image(access_token, image_path):
    """上传封面图片，获取 media_id"""
    url = f"https://api.weixin.qq.com/cgi-bin/material/add_material?access_token={access_token}&type=image"
    try:
        # 以二进制形式上传图片
        with open(image_path, "rb") as f:
            files = {"media": f}
            response = requests.post(url, files=files)
        result = response.json()
        if "media_id" in result:
            return result["media_id"]
        else:
            raise Exception(f"上传封面图失败：{result}")
    except Exception as e:
        print(f"上传封面图异常：{e}")
        return None


def create_draft(access_token, title, author, content, thumb_media_id=""):
    """创建微信公众号草稿箱内容"""
    url = f"https://api.weixin.qq.com/cgi-bin/draft/add?access_token={access_token}"

    # 草稿内容（必须是 JSON 格式，content 需为 HTML）
    draft_data = {
        "articles": [
            {
                "title": title,  # 文章标题
                "author": author,  # 作者
                "content": content,  # 文章正文（HTML 格式）
                "thumb_media_id": thumb_media_id,  # 封面图 media_id（可选）
                "show_cover_pic": 1,  # 是否显示封面图（1=显示，0=不显示）
                "need_open_comment": 1,  # 是否开启评论（1=开启）
                "only_fans_can_comment": 0,  # 是否仅粉丝可评论（0=所有人）
                "content_source_url": ""  # 原文链接（可选）
            }
        ]
    }

    headers = {"Content-Type": "application/json"}
    try:
        response = requests.post(url, data=json.dumps(draft_data, ensure_ascii=False).encode("utf-8"), headers=headers)
        result = response.json()
        if "media_id" in result:
            print(f"草稿创建成功！草稿 ID：{result['media_id']}")
            return result["media_id"]
        else:
            raise Exception(f"创建草稿失败：{result}")
    except Exception as e:
        print(f"创建草稿异常：{e}")
        return None


if __name__ == "__main__":
    # 1. 获取 Access Token
    access_token = get_access_token(APP_ID, APP_SECRET)
    if not access_token:
        exit()

    # 2. （可选）上传封面图
    thumb_media_id = ""
    if COVER_IMAGE_PATH:
        thumb_media_id = upload_cover_image(access_token, COVER_IMAGE_PATH)
        if not thumb_media_id:
            print("封面图上传失败，将尝试无封面创建草稿")

    # 3. 准备文章内容（HTML 格式）
    article_title = "Python 自动创建公众号草稿测试"
    article_author = "测试作者"
    article_content = """
    <p>这是用 Python 自动上传到公众号草稿箱的测试内容</p>
    <h2>二级标题</h2>
    <p>支持所有 HTML 标签，比如：</p>
    <ul>
        <li>列表项1</li>
        <li>列表项2</li>
    </ul>
    <img src="https://example.com/test.jpg" alt="测试图片">
    """

    # 4. 创建草稿
    create_draft(access_token, article_title, article_author, article_content, thumb_media_id)
