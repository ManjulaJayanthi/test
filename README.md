use actix_web::{get, web::Path, HttpResponse};

use crate::user::{
    user_app::fetch_userinfo_from_file, user_response::GetUserinfoFileResponse, GetUserinfoResponse,
};

#[get("/userinfo/{id}")]
pub async fn get_userinfo(_path: Path<String>) -> GetUserinfoResponse {
    println!("\n get_userinfo");
    Ok(HttpResponse::Ok().body("get_userinfo"))
}

#[get("/userinfo/file/{id}")]
pub async fn get_userinfo_from_file(path: Path<String>) -> GetUserinfoFileResponse {
    println!("\n get_userinfo_1");

    let result = fetch_userinfo_from_file(path.to_string())?;
    println!("\n result : {:?}", &result);
    Ok(HttpResponse::Ok().json(result))
}


BYOD is applicable only for Infoscians. Please refer BYOD policy. please go to Infy me--> Webapps-->Search for BYOD policy for any exception with business reasons, following approvals are required: For any exception to subcons, please follow below process. Attached legal recommendation file and subcon need to inform the requirement to Vendor with whom Sub-Con is employed. Approval from Unit Head & Unit HR is Mandatory, on reading the attached Terms. Also Please attach approval from vendor to whom subcon is employed. Max exception is for 3 months
