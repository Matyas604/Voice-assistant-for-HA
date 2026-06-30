## How to Set Up the Assistant
First of all, you'll need to have [Home Assistant](https://www.home-assistant.io/) and the [ESPHome](https://esphome.io/) extension installed. If you don't know how to do that, search online! 

First, you need to download the .yaml code and edit the Wi-Fi settings in it; enter your information so that the board can connect to your Wi-Fi! Then, simply compile the code and download it.
### How to upload the code:
Open the [ESPHome](https://web.esphome.io/) page. Connect the board to your PC via USB-C (it must be a data cable—not just a power cable). 
Then click the “Connect” button. A port selection menu will open; this may vary by computer, but on mine, the port for the ESP was ```USB JTAG/Serial Debug Unit``` on port 5. 
The board may keep restarting and disconnecting—if so, simply unplug it, hold down the BOOT button, and plug it back in, then select it again on the page. 
Then just click the “Install” button and select the .bin file. After a moment, the code will upload, and the board should connect to Wi-Fi. It will then be visible in ESPHome and HA. 
If not, check the logs and try searching online.

<p align="center">
  <img src="Assets/ESPHome site.png" alt="ESPHome site" width="400">
</p>
<p align="center">
  <img src="Assets/ESP connected.png" alt="ESP connected" width="500">
</p>

An encryption key is required to connect to HA properly. It is in the code, or, if it hasn't been changed, it is "R2iV3Cuc+P9gXfcOKZiR/pYnqE1fDyUBjXqoBMwdVm4=". 
After successfully connecting and setting up HA, you still need to configure the "Assistant pipeline" for STT conversion and TTS.

## How to Set Up the Assistant Pipeline:
At first, I said it would work great with **Gemini**—and it does work great—but I found out that the limits without paying are really high. A maximum of 20 requests per day. 
So I looked for an alternative and found **Groq**, which has a lot of models, but most importantly, it also supports **STT**, and their limits are about 30 minutes of pure recording time per day for **STT** and, depending on the model, about 50–60 requests per day for answer. 
It’s calculated in tokens, so depending on the complexity of the command or question—the most complex or lengthy ones use **4,000 or 5,000 tokens**— **HA** sometimes sends them twice, so that’s about **10,000 tokens** for a complex command. The daily limit is **500,000 tokens**, so there’s a big reserve. 
Plus, this applies only to individual models, not overall, so once you’ve used up your limit, you just need to switch to a different model—and you can add more models in **HA**, unlike with **Gemini**. So, in my opinion, Groq is much better, but most importantly, it’s completely free for this. 
It also supports a huge amount of languages, so it doesn’t have to be just in **English**; I personally use **Czech**, and it works great.

First, you need to download the **Groq** integration for **HA**, since it isn't included automatically. Simply go to **HACS** to download it there, or download the **GitHub** library [here](https://github.com/barneyonline/ha-groq).

![image](https://stasis.hackclub-assets.com/images/1782805588892-2ct92n.png)

After successfully downloading and restarting **HA**, the integration will appear among the others as usual; just look for it in the list.

![image](https://stasis.hackclub-assets.com/images/1782805880979-4gkj0i.png)

Now you need to create an account with **Groq** and generate an **API** key [here](https://console.groq.com/home). You can see them [here](https://console.groq.com/keys).

![image](https://stasis.hackclub-assets.com/images/1782806044715-qklcc5.png)

Now all you have to do is copy it, add the **Groq** integration in **HA**, and paste the **API** key there.

![image](https://stasis.hackclub-assets.com/images/1782806130306-yjlanw.png)

Now that you've successfully created it, all you need to do is add **STT**, the **AI** conversation model, and **TTS** one by one. I recommend following my settings because they work. For the **AI** conversation model, I recommend 
``` meta-llama/llama-4-scout-17b-16e-instruct ```
 because it has the highest daily usage limit; for STT, it doesn’t really matter—both have the same limits. And **TTS** is only available in English or Arabic.

![image](https://stasis.hackclub-assets.com/images/1782806418301-w5dx57.png)
 
![image](https://stasis.hackclub-assets.com/images/1782806451370-c5muqf.png)

![image](https://stasis.hackclub-assets.com/images/1782806481856-872tjd.png)

![image](https://stasis.hackclub-assets.com/images/1782806511729-mkbzf6.png)

If your language isn't listed in **STT**, first check whether **Groq STT** supports that language. If it does, simply go to ``` /home-assistant/config/custom_components/groq/const.py ```
 and in the section 
```STT_LANGUAGE_OPTIONS = [ ```
, add your language in the correct format. Save the file and restart HA. For example, this is how I added Czech, because Groq supports it but it wasn’t specified in the integration.

![image](https://stasis.hackclub-assets.com/images/1782806908491-fp4wfd.png)

```
    {"value": "cs-CZ", "label": "Czech"},
```
Once everything is set up, just go to **HA** Settings, Voice Assistants, and add a new assistant in Assist. Fill in all the fields with the **STT**, **TTS**, and **AI** conversation settings you configured, and add a name.

![Snímek obrazovky 2026-06-30 101927](https://stasis.hackclub-assets.com/images/1782807616660-fburn4.png)

Now all you have to do is select this configured chat assistant in the assistant settings.

![image](https://stasis.hackclub-assets.com/images/1782807752527-g1zr7b.png)

That's it—the assistant will now answer questions and control your home. Once the limit is reached, this model will no longer work, but you can switch to another one. The limit resets after 24 hours.The model limits are listed [here](https://console.groq.com/docs/rate-limits).
