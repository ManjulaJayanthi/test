mod user_route;
pub use user_route::{get_userinfo, get_userinfo_from_file};

mod user_response;
pub use user_response::{GetUserinfoResponse, UserInfoError};

mod user_test;

mod user_app;

mod user_model;
pub use user_model::UserinfoFileResponse;


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
