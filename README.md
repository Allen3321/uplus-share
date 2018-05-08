
小程序登录：
http://www.jb51.net/article/113834.htm


package com.minitrip.common.util;

import com.squareup.okhttp.OkHttpClient;
import com.squareup.okhttp.Request;
import com.squareup.okhttp.Response;
import com.squareup.okhttp.ResponseBody;
import lombok.val;
import net.sf.json.JSONObject;
import org.apache.log4j.Logger;

public class WeChatUtil {

    private static final Logger logger =  Logger.getLogger(WeChatUtil.class);
    private static final String APP_ID = "wxc23efa9a8c00c871";
    private static final String SECRET = "998ab0ce210d5f8d842f261fb8d9d46a";
    private static final String GRANT_TYPE = "authorization_code";
    private static final OkHttpClient client = new OkHttpClient();


    /**
    客户端获取code发送给服务端，服务端通过code想微信发送请求获取用户信息
    */
    public static String getOpenId(String code){
        String openId = null;
        try {
            String authUrl = "https://api.weixin.qq.com/sns/jscode2session?appid="
                    + APP_ID + "&secret=" + SECRET + "&js_code=JS_CODE&grant_type=" + GRANT_TYPE;
            authUrl = authUrl.replace("JS_CODE", code);
            Request request = new Request.Builder()
                    .url(authUrl)
                    .build();
            Response response = client.newCall(request).execute();
            if (response.isSuccessful()){
                ResponseBody body = response.body();
                val bytes = body.bytes();
                JSONObject jsonObject = JSONObject.fromObject(new String(bytes));
                if(jsonObject.has("openid")){
                    openId = jsonObject.getString("openid");
                }else {
                    logger.error("WeChatUtil Class getOpenId method err, response errcode: "
                            + jsonObject.get("errcode") + ", errmsg: " + jsonObject.get("errmsg"));
                }
            }
        }catch (Exception e){
            e.printStackTrace();
            logger.error("WeChatUtil Class getOpenId method err: " + e);
        }
        return openId;
    }

    public static void main(String[] args) {
        String openId = WeChatUtil.getOpenId("001fA2fP0UUf7c2W8qhP0k4ReP0fA2fL");
        System.out.println(openId);
    }
}
