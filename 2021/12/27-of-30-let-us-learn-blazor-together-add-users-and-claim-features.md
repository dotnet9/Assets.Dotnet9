---
title: (27/30)大家一起学Blazor：添加用户和Claim功能
slug: 27-of-30-let-us-learn-blazor-together-add-users-and-claim-features
description: 前面说过`ASP.NET Core Identity` 是基于`Claim` 的验证，而`Role` 就是类型为`Role` 的`Claim`
date: 2021-12-25 11:08:26
copyright: Reprinted
author: StrayaWorker
originalTitle: (27/30)大家一起学Blazor：添加用户和Claim功能
originalLink: https://ithelp.ithome.com.tw/articles/10273602
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums:
    - 一起学Blazor系列
categories: 
    - Blazor
tags: 
    - Blazor
    - ASP.NET Core
    - 学Blazor
---

前面说过`ASP.NET Core Identity` 是基于`Claim` 的验证，而`Role` 就是类型为`Role` 的`Claim`，`ASP.NET Framework Identity` 时代只有`Role` 验证，`Claim` 是`ASP.NET Core Identity` 才出现的，目的是为了取得外部程序如`Facebook`、`Twitter` 等等`第三方的授权`，如此一来用户就不用在不同平台注册重复帐号。

而`Claim` 其实就只是一组`ClaimType`、`ClaimValue` 的字符串组合，通常不会像`Role` 用一个页面去管理并指派给`User`，而是以`User` 页面管理并新增或移除`User` 底下的`Claim`，所以今天来实现`User` 页面。

首先一样需要`ViewModel` 和数据存取层，因为做的事情一样，就不多说明了。

`User` 的`ViewModel`。

```C#
namespace BlazorServer.ViewModels;

public class CustomUserViewModel
{
	public CustomUserViewModel()
	{
		Claims = new List<string>();
	}

	public string? UserId { get; set; }

	public string? UserName { get; set; }

	public string? Email { get; set; }

	public List<string>? Claims { get; set; }
}
```

承载单一`Claim` 的`ViewModel`

```C#
namespace BlazorServer.ViewModels;

public class CustomUserClaimViewModel
{
	public string? ClaimType { get; set; }
	public bool IsSelected { get; set; }
}
```

承载`User` 下`Claim` 的`ViewModel`

```C#
namespace BlazorServer.ViewModels;

public class CustomUserClaimsViewModel
{
	public CustomUserClaimsViewModel()
	{
		Claims = new List<CustomUserClaimViewModel>();
	}

	public string? UserId { get; set; }

	public List<CustomUserClaimViewModel> Claims { get; set; }
}
```

因为`Claim` 不像`User` 本来就注册了，也不像`Role` 会让用户自己定义，所以这边先建立好几组跟`User` 权限有关的`Claim`。

```C#
using System.Security.Claims;

namespace BlazorServer.Models;

public static class ClaimsStore
{
	public static List<Claim> AllClaims = new()
	{
		new Claim("ManageUser", string.Empty),
		new Claim("CreateUser", string.Empty),
		new Claim("EditUser", string.Empty),
		new Claim("DeleteUser", string.Empty)
	};
}
```

页面`IUserRepository`

```C#
using BlazorServer.Models;
using BlazorServer.ViewModels;

namespace BlazorServer.Repository;

public interface IUserRepository
{
	Task<ResultViewModel> DeleteUserAsync(string userId);
	Task<ResultViewModel> EditUserAsync(CustomUserViewModel model);
	Task<CustomUserViewModel> GetUserAsync(string userId);
	Task<List<CustomUserViewModel>> GetUsersAsync();
	Task<CustomUserClaimsViewModel> EditClaimsInUserAsync(string userId);
	Task<ResultViewModel> EditClaimsInUserAsync(CustomUserClaimsViewModel model);
}
```

实现`UserRepository`，如果还记得`RoleRepository.EditUsersInRoleAsyncPost` 方法的话，当时是用两个变量分开存储`Role.Id`及`List<CustomUserRoleViewModel> model`，这边编辑`User` 下`Claim` 的`Post` 方法跟`Role` 不同，是再用一个 ViewModel `CustomUserClaimsViewModel` 去承载数据，本质上并无差别。

```C#
using System.Security.Claims;
using BlazorServer.Models;
using BlazorServer.ViewModels;
using Microsoft.AspNetCore.Identity;

namespace BlazorServer.Repository.Implement;

public class UserRepository : IUserRepository
{
	private readonly UserManager<IdentityUser> _userManager;

	public UserRepository(UserManager<IdentityUser> userManager)
	{
		_userManager = userManager;
	}

	public async Task<List<CustomUserViewModel>> GetUsersAsync()
	{
		var customUsers = _userManager.Users.Select(user => new CustomUserViewModel
			{ UserId = user.Id, UserName = user.UserName, Email = user.Email }).ToList();

		return await Task.Run(() => customUsers);
	}

	public async Task<CustomUserViewModel> GetUserAsync(string userId)
	{
		var user = await _userManager.FindByIdAsync(userId);
		var userClaims = await _userManager.GetClaimsAsync(user);
		var result = new CustomUserViewModel
		{
			UserId = user.Id,
			UserName = user.UserName,
			Email = user.Email,
			Claims = userClaims.Select(x => $"{x.Type} : {x.Value}").ToList()
		};
		return result;
	}

	public async Task<ResultViewModel> EditUserAsync(CustomUserViewModel model)
	{
		var user = await _userManager.FindByIdAsync(model.UserId);

		if (user == null)
		{
			return new ResultViewModel
			{
				Message = $"找不到 Id 为{model.UserId} 的用户",
				IsSuccess = false
			};
		}

		user.UserName = model.UserName;
		user.Email = model.Email;
		var result = await _userManager.UpdateAsync(user);
		if (result.Succeeded)
		{
			return new ResultViewModel
			{
				Message = "用户更新成功！",
				IsSuccess = true
			};
		}

		return new ResultViewModel
		{
			Message = "用户更新失败！",
			IsSuccess = false
		};
	}

	public async Task<ResultViewModel> DeleteUserAsync(string userId)
	{
		var user = await _userManager.FindByIdAsync(userId);

		if (user == null)
		{
			return new ResultViewModel
			{
				Message = $"找不到 Id 为 {userId} 的用户",
				IsSuccess = false
			};
		}

		var result = await _userManager.DeleteAsync(user);
		if (result.Succeeded)
		{
			return new ResultViewModel
			{
				Message = "用户刪除成功！",
				IsSuccess = true
			};
		}

		return new ResultViewModel
		{
			Message = "用户刪除失败！",
			IsSuccess = false
		};
	}

	public async Task<CustomUserClaimsViewModel> EditClaimsInUserAsync(string userId)
	{
		var user = await _userManager.FindByIdAsync(userId);
		var claims = await _userManager.GetClaimsAsync(user);
		var model = new CustomUserClaimsViewModel
		{
			UserId = userId
		};

		foreach (var claim in ClaimsStore.AllClaims)
		{
			var userClaim = new CustomUserClaimViewModel
			{
				ClaimType = claim.Type
			};

			if (claims.Any(c => c.Type == claim.Type && c.Value == "true"))
			{
				userClaim.IsSelected = true;
			}

			model.Claims.Add(userClaim);
		}

		return model;
	}

	public async Task<ResultViewModel> EditClaimsInUserAsync(CustomUserClaimsViewModel model)
	{
		var user = await _userManager.FindByIdAsync(model.UserId);
		var claims = await _userManager.GetClaimsAsync(user);
		var result = await _userManager.RemoveClaimsAsync(user, claims);

		if (!result.Succeeded)
		{
			return new ResultViewModel
			{
				Message = "无法移除用户的 Claim！",
				IsSuccess = false
			};
		}

		result = await _userManager.AddClaimsAsync(user,
			model.Claims.Select(c => new Claim(c.ClaimType!, c.IsSelected ? "true" : "false")));

		if (!result.Succeeded)
		{
			return new ResultViewModel
			{
				Message = "无法將指定的 Claim 分配给用户！",
				IsSuccess = false
			};
		}

		return new ResultViewModel
		{
			Message = "分配 Claim 成功",
			IsSuccess = true
		};
	}
}
```

再去`Program.cs`注册

```C#
builder.Services.AddScoped<IUserRepository, UserRepository>();
```

然后就是前端页面呈现。

`UserManagement.razor.cs`

```C#
using BlazorServer.Repository;
using BlazorServer.Shared;
using BlazorServer.ViewModels;
using Microsoft.AspNetCore.Components;
using Microsoft.JSInterop;
using JsonSerializer = System.Text.Json.JsonSerializer;

namespace BlazorServer.Pages.RolesManagement;

public partial class UserManagement
{
	[Inject] protected IUserRepository? UserRepository { get; set; }
	[Inject] protected NavigationManager? NavigationManager { get; set; }
	[Inject] protected IJSRuntime? Js { get; set; }
	private JsInteropClasses? _jsClass;
	public List<CustomUserViewModel> Users { get; set; } = new();

	protected override async Task OnInitializedAsync()
	{
		await LoadData();
		_jsClass = new JsInteropClasses(Js!);
	}

	private async Task LoadData()
	{
		Users = await UserRepository!.GetUsersAsync();
	}

	private async Task EditUser(string userId)
	{
		NavigationManager!.NavigateTo($"UserManagement/EditUser/{userId}");
		await Task.CompletedTask;
	}

	private async Task DeleteUser(string userId)
	{
		var sweetConfirm = new SweetConfirmViewModel()
		{
			RequestTitle = $"是否确定删除用户{userId}？",
			RequestText = "这个操作不可逆",
			ResponseTitle = "刪除成功",
			ResponseText = "用户被刪除了",
		};
		var jsonString = JsonSerializer.Serialize(sweetConfirm);
		var result = await _jsClass!.Confirm(jsonString);
		if (result)
		{
			var deleted = await UserRepository!.DeleteUserAsync(userId);
			if (deleted.IsSuccess)
			{
				await LoadData();
			}
			else
			{
				await _jsClass!.Alert(deleted.Message!);
			}
		}
	}
}
```

`UserManagement.razor`

```html
@page "/UserManagement/UserList"

<h1>所有用户</h1>

@if (Users.Any()) {
<NavLink
  class="btn btn-primary mb-3"
  href="Identity/Account/Register"
  Match="NavLinkMatch.All"
>
  新增用户
</NavLink>

foreach (var user in Users) {
<div class="card mb-3 w-25">
  <div class="card-header">User Id : @user.UserId</div>
  <div class="card-body">
    <h5 class="card-title">@user.UserName</h5>
  </div>
  <div class="card-footer">
    <button
      type="button"
      class="btn btn-primary"
      @onclick="() => EditUser(user.UserId)"
    >
      编辑用户
    </button>
    <button
      type="button"
      class="btn btn-danger"
      @onclick="() => DeleteUser(user.UserId)"
    >
      刪除用户
    </button>
  </div>
</div>
} } else {
<div class="card w-25">
  <div class="card-header">还沒有用户</div>
  <div class="card-body">
    <h5 class="card-title">点击底下的按钮新增用户</h5>
    <NavLink
      class="btn btn-primary"
      href="Identity/Account/Register"
      Match="NavLinkMatch.All"
    >
      新增用户
    </NavLink>
  </div>
</div>
}
```

`EditUser.razor.cs`

```C#
using BlazorServer.Repository;
using BlazorServer.ViewModels;
using Microsoft.AspNetCore.Components;

namespace BlazorServer.Pages.RolesManagement;

public partial class EditUser
{
	[Inject] protected IUserRepository? UserRepository { get; set; }
	[Inject] protected NavigationManager? NavigationManager { get; set; }
	public CustomUserViewModel User { get; set; } = new();
	[Parameter] public string? UserId { get; set; }

	protected override async Task OnInitializedAsync()
	{
		var result = await UserRepository!.GetUserAsync(UserId!);
		User = new CustomUserViewModel
		{
			UserId = result.UserId,
			UserName = result.UserName,
			Claims = result.Claims
		};
	}

	private async Task EditRole()
	{
		await UserRepository!.EditUserAsync(User);
		NavigationManager!.NavigateTo("/UserManagement/UserList");
	}

	public void EditUsersInRole()
	{
		NavigationManager!.NavigateTo($"/UserManagement/EditClaimsInUser/{UserId}");
	}

	public void Cancel()
	{
		NavigationManager!.NavigateTo($"/UserManagement/UserList");
	}
}
```

`EditUser.razor`

```html
@page "/UserManagement/EditUser/{UserId}"

<EditForm class="mt-3" Model="User" OnValidSubmit="EditRole">
  <DataAnnotationsValidator />
  <ValidationSummary />
  <div class="form-group row">
    <label for="RoleName" class="col-sm-1 col-form-label">用户名称</label>
    <div class="col-sm-3">
      <InputText
        @bind-Value="User.UserName"
        id="RoleName"
        class="form-control"
        placeholder="用户名称"
      ></InputText>
    </div>
  </div>

  <div class="card mb-3 w-50">
    <div class="card-header">
      <h3>用户底下的 Claim</h3>
    </div>
    <div class="card-body">
      @if (User.Claims.Any()) { foreach (var claim in User.Claims) {
      <h5 class="card-title">@claim</h5>
      } } else {
      <h5 class="card-title">目前该用户沒有任何 Claim</h5>
      }
    </div>
    <div class="card-footer">
      <button type="submit" class="btn btn-primary">更新用户</button>
      <button type="button" class="btn btn-info" @onclick="EditUsersInRole">
        新增或移除该用户底下的 Claim
      </button>
      <button type="button" class="btn btn-danger" @onclick="Cancel">
        取消
      </button>
    </div>
  </div>
</EditForm>
```

`EditClaimsInUser.razor.cs`

```C#
using BlazorServer.Repository;
using BlazorServer.Shared;
using BlazorServer.ViewModels;
using Microsoft.AspNetCore.Components;
using Microsoft.JSInterop;

namespace BlazorServer.Pages.RolesManagement;

public partial class EditClaimsInUser
{
	[Inject] protected IUserRepository? UserRepository { get; set; }
	[Inject] protected NavigationManager? NavigationManager { get; set; }
	[Inject] protected IJSRuntime? Js { get; set; }
	private JsInteropClasses? _jsClass;
	[Parameter] public string? UserId { get; set; }
	public CustomUserClaimsViewModel UserClaimViewModel { get; set; } = new CustomUserClaimsViewModel();

	protected override async Task OnInitializedAsync()
	{
		await LoadData();
		_jsClass = new JsInteropClasses(Js!);
	}

	private async Task LoadData()
	{
		UserClaimViewModel = (await UserRepository!.EditClaimsInUserAsync(UserId!));
	}


	public async Task HandleValidSubmit()
	{
		var result = await UserRepository!.EditClaimsInUserAsync(UserClaimViewModel);

		if (result.IsSuccess)
		{
			NavigationManager!.NavigateTo($"/UserManagement/EditUser/{UserId}");
		}
		else
		{
			await _jsClass!.Alert(result.Message!);
		}
	}

	public void Cancel()
	{
		NavigationManager!.NavigateTo($"/UserManagement/EditUser/{UserId}");
	}
}
```

`EditClaimsInUser.razor`

```html
@page "/UserManagement/EditClaimsInUser/{UserId}"

<EditForm Model="UserClaimViewModel" OnValidSubmit="HandleValidSubmit">
  <DataAnnotationsValidator />
  <ValidationSummary />
  <div class="card">
    <div class="card-header">
      <h2>从用户新增或移除 Claim</h2>
    </div>
    <div class="card-body">
      @foreach (var claim in UserClaimViewModel.Claims) {
      <div class="form-check m-1">
        <label class="form-check-label">
          <InputCheckbox @bind-Value="@claim.IsSelected"></InputCheckbox>
          @claim.ClaimType
        </label>
      </div>
      }
    </div>
    <div class="card-footer">
      <button type="submit" class="btn btn-primary">更新</button>
      <button type="button" class="btn btn-danger" @onclick="@Cancel">
        取消
      </button>
    </div>
  </div>
</EditForm>
```

最后再去`NavMenu.razor`加入`NavLink`。

```html
<li class="nav-item px-3">
  <NavLink
    class="nav-link"
    href="UserManagement/UserList"
    Match="NavLinkMatch.All"
  >
    <span class="bi bi-people h4 p-2 mb-0" aria-hidden="true"></span> Users
  </NavLink>
</li>
```

这样就有简单的`User` 及`Claim` 的`CRUD` 页面了。

`引用：`

1. [Manage user claims in asp net core](https://www.youtube.com/watch?v=5XA4Z-SOif8&list=PL6n9fhu94yhVkdrusLaQsfERmL_Jh4XmU&index=93)
2. [Claim type and claim value in claims policy based authorization in asp net core](https://www.youtube.com/watch?v=I2wgxzLbESA&list=PL6n9fhu94yhVkdrusLaQsfERmL_Jh4XmU&index=98)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**
